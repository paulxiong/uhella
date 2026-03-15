# Taste: On-Device Personalization with a Transformer You Actually Own

> A deep dive into building a personalized photo recommendation engine that trains on your iPhone, runs in milliseconds, and lets you share your aesthetic as a file.

---

## The Problem with Recommendation Engines You Don't Own

Every major recommendation system today is a black box running on someone else's server. It knows your behavior, but you can't inspect it, reset it, export it, or give it to a friend. The model is the platform's property — not yours.

**Taste** inverts this. The model lives on your device. It trains on your taps. It's a file you can export, share, import, and version. And it runs fast enough that every single like triggers a training step in real time.

This article walks through the full architecture: how the transformer is designed for on-device constraints, why "clicks-is-all-you-need" works as a training signal, how the feed switches between random exploration and AI exploitation like a reinforcement learning agent, and what makes taste-sharing a genuinely new primitive.

---

## Architecture Overview

```
User tap
    │
    ▼
TapTracker          ← records every liked photo (newest-first queue)
    │
    ▼
ExposureTracker     ← records every shown photo (skipped = implicit negative)
    │
    ├── Positive target: the tapped photo
    └── Negative samples: 12 recent skipped photos
    │
    ▼
Training Isolate    ← background Dart isolate, never blocks UI
    │
    ▼
TransformerModel    ← 256-dim on-device transformer, updates _outputProjection
    │
    ▼
ModelWeights DB     ← gzip-compressed int8/f32 hybrid, ~3-4 MB on disk
    │
    ├── Inference path → cosine similarity → self-score per photo
    └── Snapshot path  → export as .taste file, share with friends
```

---

## The Transformer: Designed for Mobile Constraints

### Dimension Reduction as the Core Trade-off

The input to the system is a **2048-dimensional ResNet50 feature vector** for each photo. Running a full transformer at 2048 dimensions on a phone is impractical — the attention matrices alone would be 2048×2048×4 heads = 64M parameters just for one layer.

The solution is a **bottleneck projection**:

```
Photo embeddings (2048-dim)
        │
        ▼  _inputProjection  [2048 × 256]
Internal representation (256-dim on iOS, 512-dim on Android)
        │
        ▼  Multi-head self-attention (4 heads)
        │
        ▼  Feed-forward network (2× hidden on iOS, 4× on Android)
        │
        ▼  _outputProjection  [256 × 2048]
Prediction (2048-dim, same space as input)
```

This reduces the working memory from ~64MB to ~12MB uncompressed, and to ~3–4MB after quantization. The key insight is that you don't need to do all your thinking at 2048 dimensions — you compress, reason, decompress.

### The Two Projection Matrices Are Not Symmetric

`_inputProjection` and `_outputProjection` are not transposes of each other. They are learned independently:

- `_inputProjection` learns to extract the **semantically useful dimensions** from ResNet features
- `_outputProjection` is the **primary trained weight** — it learns to map internal transformer output back into the specific region of embedding space that corresponds to what the user likes

This asymmetry is intentional. In the normal training path (`trainOnBatch`), only `_outputProjection` is updated. The attention and FFN weights stay frozen. This is a deliberate design decision: the attention mechanism acts as a **fixed feature extractor over the sequence**, while the output projection is the learnable "taste head" that gets shaped by user behavior.

### Residual Connection: Staying in ImageNet Space

The inference output applies a critical residual:

```dart
final_output[i] = inputVector[i] + 0.5 * purePrediction[i]
```

This means the model never predicts a completely arbitrary embedding. It always stays **50% anchored to the input photo's own embedding**. The learned component (`purePrediction`) provides a *delta* — a push toward what you tend to like, from wherever the current photo sits in semantic space.

Why does this matter? ResNet50 embeddings are trained on ImageNet. They have a specific geometry — nearby points are semantically similar images. If the model were free to predict arbitrary 2048-dim vectors, it could escape this geometry entirely and produce predictions that don't correspond to any real image concept. The residual prevents this, keeping every prediction interpretable within the original embedding space.

The training objective reflects this exactly:

```
We want: input + 0.5 * purePrediction ≈ target
So train toward: purePrediction ≈ (target - input) / 0.5
```

### Numerical Safety Everywhere

On-device training without a server-side safety net requires explicit bounds at every step:

| Guard | Value | Purpose |
|-------|-------|---------|
| Weight clamp | ±5.0 | Prevents weight explosion across many taps |
| Error clamp | ±5.0 | Prevents single-step catastrophic updates |
| Gradient clamp | ±1.0 | Bounds individual gradient values |
| exp() input clamp | ±50.0 | Prevents NaN in softmax |
| tanh() input clamp | ±10.0 | Prevents saturation in GELU |

---

## "Clicks-Is-All-You-Need": The Training Philosophy

### One Tap, One Training Step

The entire training pipeline collapses to a single principle: **every like is a supervised training example**. No explicit labels, no manual annotation, no periodic batch jobs. The user's tap *is* the label.

When a user taps photo **T** after having seen photos **A, B, C**:

- **Target embedding**: ResNet50 embedding of photo T
- **Input sequence**: [C, B, A] (newest first, up to 10 photos)
- **Negative samples**: up to 12 photos the user scrolled past between taps (from ExposureTracker)

The model is trained to answer: *"Given the sequence of photos I've liked, predict the embedding of the next photo I'll like."*

### Why Skipped Photos Are Valuable

The ExposureTracker maintains an in-memory queue of up to 400 photos the user has been shown. Photos between two taps — the ones the user saw but didn't like — are **implicit negatives**. They're not random noise; they're photos from the user's actual library that the user actively chose to skip.

This makes them **hard negatives**: photos that are potentially similar to liked content (they came from the same library, shown in the same session), but that the user explicitly passed over. Training against these produces a much sharper decision boundary than training against random images from the internet.

### Cosine Similarity as the Loss

The loss function is simply:

```
loss = 1.0 - cosine_similarity(prediction, target)
```

Range: 0 (perfect) to 2 (opposite). This is a cosine loss in the normalized embedding space, which has a clean geometric interpretation: **push the model's output vector toward the target direction on the unit hypersphere**.

The gradient targets a "pure prediction" computed from the residual relationship:

```
desiredPure = (target - input) / 0.5
```

Then error = `desiredPure - currentPure`, clipped to ±5.0, used to update `_outputProjection` via a rank-1 outer product update:

```
ΔW[j,i] = error[i] × reducedPrediction[j] × scale
```

where `scale = lr / (||reducedPrediction|| × 10)`. The denominator normalizes the update by the activation magnitude, preventing large activations from causing proportionally large weight changes.

### Training in a Background Isolate

Flutter's Dart runtime is single-threaded per isolate. Running 50 epochs of matrix multiplication on the main thread would drop frames. The entire training pipeline runs in a **dedicated background isolate** — a separate Dart execution context with its own memory space.

Communication happens through typed message passing:

```
Main Isolate                     Training Isolate
     │                                 │
     │─── TrainingMessage ────────────►│
     │    (targetAssetId,              │
     │     inputSequence,              │  trainOnBatch()
     │     negativeAssetIds)           │  _updateWeights()
     │                                 │  saveWeights()
     │◄── TrainingResult ─────────────│
     │    (weightUpdated, steps)       │
     │                                 │
     ▼                                 │
reloadWeights()                        │
invalidateCache()                      │
emitTasteUpdate()                      │
```

The UI never waits for training. Training results arrive asynchronously and trigger a cache invalidation + recommendation refresh.

---

## Self-Score: How a Photo Knows If You'll Like It

### The Inference Path

Given the user's recent tap history, the model predicts an embedding vector representing "what I'd like to see next." Self-score measures how close any given photo is to that prediction:

```
1. Get tapped sequence: [photo_n, photo_{n-1}, ..., photo_{n-9}]
2. Run predict(sequence) → predicted_embedding (2048-dim)
3. For candidate photo C with embedding E_C:
   self_score = cosine_similarity(predicted_embedding, E_C)
```

A self-score above 0.60 means the photo is likely to be recommended. Below 0.60, it stays in the random feed.

### Self-Score as a Live Feedback Signal

The self-score is computed dynamically as the user's context changes. After each like, the tap sequence updates, the model updates (via training), and self-scores across the entire photo library shift accordingly. This creates a **real-time preference signal** that can be visualized per photo.

This is qualitatively different from a static ranking. The score isn't "the system thinks you'll like this" — it's "given your last 10 likes, the model predicts something this similar to this photo."

---

## Random Feed vs. AI Feed: Reinforcement Learning in Disguise

### Two Feeds, One Purpose

The app maintains two feed modes:

- **Random feed**: Photos sampled without regard to model predictions. Exploration.
- **AI feed**: Photos filtered by self-score ≥ 0.60, sorted by predicted similarity. Exploitation.

This mirrors the classic **explore/exploit dilemma** from reinforcement learning.

### Why You Need Both

A pure AI feed has a well-known failure mode: **filter bubbles**. The model learns from what the user has liked, then shows more of the same, which gets liked, making the model even narrower. You converge to a local optimum that may not be a global preference.

A pure random feed has the opposite problem: most photos are irrelevant, the user's attention isn't captured, and the system feels useless.

The two-feed architecture solves this by treating them as complementary:

```
Random Feed                    AI Feed
─────────────                  ─────────────
"Go far"                       "Go near"
Exploration                    Exploitation
New discovery                  Preference reinforcement
Training signal diversity      Training signal density
Implicit negatives             High-value positives
```

When the user browses the random feed and doesn't tap → they're **defining boundaries** (what they don't like). When they browse the AI feed and tap → they're **reinforcing the center** of their taste.

### The Switching Logic

The system starts in random feed and switches to AI feed once the model is meaningfully trained:

```
Conditions to switch to AI feed:
  trainingSteps > 0              ← model has been trained at least once
  valid tap sequence exists      ← enough context for a prediction
  recommendations exist          ← at least one photo above 0.60 threshold
  ExposureTracker has negatives  ← enough skipped photos for contrast
```

If the AI feed runs dry (no photos above threshold), it falls back to random feed automatically. This prevents the worst UX failure: showing the user nothing.

### The RL Framing

Think of it this way:

| RL Concept | In This System |
|-----------|---------------|
| Agent | The recommendation model |
| State | User's current tap sequence |
| Action | Show this photo to the user |
| Reward | User taps it (positive) or skips (negative) |
| Policy | AI feed (exploitation of learned preferences) |
| Exploration | Random feed (discovers new preference regions) |
| Value function | Self-score (estimated probability of a tap) |

The system doesn't implement formal RL (no Q-learning, no policy gradient), but the two-feed structure captures the essential dynamics. The random feed explores the photo space, collects reward signals (taps and skips), and the AI feed exploits what's been learned.

The key architectural choice that makes this work: **every shown photo — including random feed photos — is tracked as a potential negative sample**. The ExposureTracker doesn't distinguish between AI feed and random feed exposures. Both contribute to defining taste.

---

## Taste as a Shareable File

### The Model IS the Taste

Because the entire personalization state lives in a ~3-4MB file (gzip-compressed int8/f32 hybrid weights), it becomes natural to treat taste as a transferable artifact:

```json
{
  "snapshot_name": "1234_3000",
  "model_name": "transformer",
  "weights": "<base64_encoded_binary>",
  "weights_encoding": "base64:int8_gzip",
  "training_steps": 3000,
  "pinned": 1
}
```

Import this file → instantly browse through the lens of someone else's aesthetic. Your feed reorganizes itself around their trained preferences.

### Snapshot Versioning

The system maintains a full history of taste snapshots, named by a 4-digit code plus training step count (e.g., `1234_3000`). You can:

- **Pin** a favorite taste (doesn't get overwritten by continued training)
- **Switch** between tastes instantly (just activate a different snapshot)
- **Export** any snapshot as a `.taste` file
- **Import** a friend's taste and browse through their eyes

This creates a fundamentally new social primitive: **aesthetic sharing without social graphs**. You're not following a person — you're temporarily inhabiting their trained model.

### What "3000 Training Steps" Means

The training step counter (encoded in the snapshot name) gives a rough signal of how "mature" a taste is:

- **0 steps**: Fresh model, random weights. Random feed dominates.
- **50–200 steps**: Early taste formation. AI feed starts appearing.
- **1000+ steps**: Stable aesthetic. High-confidence recommendations.
- **5000+ steps**: Highly specialized. Narrow but precise taste.

You can use training steps to evaluate whether an imported taste has enough signal to be useful.

---

## Weight Serialization: Hybrid Quantization

### Why Not Pure int8?

The projection matrices (`_inputProjection` and `_outputProjection`) are the most sensitive weights in the model. They handle the 2048↔256 dimensional bridge. Quantizing them to int8 introduces rounding errors at the dimensional transition that compound across every inference.

The attention and FFN weights, operating entirely within the 256-dim space, are much more tolerant of quantization noise. Small errors in the attention scores produce small errors in the weighted average of values — the softmax and residual connections act as natural smoothing.

### The Hybrid Format

```
InputProjection:  float32  (2048 × 256 × 4 bytes = 2.1 MB)
OutputProjection: float32  (256 × 2048 × 4 bytes = 2.1 MB)
QueryWeights:     int8     (256 × 256 × 1 byte = 65 KB)
KeyWeights:       int8     (65 KB)
ValueWeights:     int8     (65 KB)
OutputWeights:    int8     (65 KB)
FFN1:             int8     (256 × 512 × 1 byte = 131 KB)
FFN2:             int8     (131 KB)

Before gzip:  ~4.7 MB
After gzip:   ~2-3 MB
```

Per-tensor quantization uses each tensor's own scale factor:

```
scale = max_abs_value / 127.0
quantized = round(value / scale).clamp(-127, 127)
```

This is significantly better than global quantization — a tensor whose values are all near 0.01 gets its own scale, rather than being swamped by the scale of a tensor with values near 5.0.

---

## Why This Design Is Unique

### 1. The Model Is Yours

Centralized recommendation engines are trained on aggregate data from millions of users. Your signal is diluted by everyone else's. The taste model is trained **only on your taps, only on your device**. No user's behavior ever influences another user's model.

### 2. Instant Feedback Loop

The training-to-inference loop closes in milliseconds. You like a photo → training fires in the background → within a few seconds, your feed has shifted. This is a tighter feedback loop than any server-side system (which typically has delays of hours or days for recommendation updates).

### 3. Compositional Aesthetic Units

Snapshots make taste **compositional**. You can train a taste on architectural photography, export it, then train a separate taste on food photography, and switch between them. Each snapshot is a distinct aesthetic filter over the same photo library.

### 4. Privacy by Architecture

There is no telemetry path. The model doesn't communicate with any server. Training happens locally, weights stay local, and sharing is a deliberate act (you explicitly export and send the file). Privacy isn't a policy — it's a structural property.

### 5. The RL Duality of Random and AI Feed

Most recommendation systems have a single feed with an explore/exploit trade-off baked into a single ranked list. Separating them into two distinct modes makes the trade-off **visible and controllable**. The user doesn't need to understand RL — they just know "this feed surprises me" vs "this feed knows me."

---

## Limitations and Open Questions

**Attention weights are frozen in normal training.** Only `_outputProjection` is updated per tap. This means the attention mechanism — the part that could learn *how to combine* a sequence of liked photos — never specializes to the user. It remains at random initialization. This is an intentional trade-off (simpler training, fewer parameters to update), but it caps expressivity.

**Sequential context is shallow.** The model sees 10 previous taps. This is enough for short-term preference within a session, but doesn't capture longer-term aesthetic arcs (your taste in January vs. July).

**No cross-device sync.** The model lives on the device where you trained it. Sharing between devices requires explicit snapshot export/import.

**iOS GPU training disabled.** The native Metal/MPS path was designed to accelerate training but was disabled because the simplified linear model it implements is architecturally incompatible with the Dart transformer — updating projection weights via the wrong gradient degrades non-target photo scores. The correct fix (replicating full transformer in Metal) is complex enough to defer.

---

## Summary

The taste system is a bet on a specific architectural thesis:

> **A small, on-device transformer, trained purely on user taps with implicit negative sampling, running in a two-mode feed that acts as an explore/exploit loop, produces a personalized recommendation engine that is private, fast, and exportable.**

The pieces are individually well-known: transformers, cosine similarity search, RL-style exploration. What's new is the combination — and specifically the decision to make the model a **first-class artifact** that the user owns, versions, and shares, rather than a side effect of platform telemetry.

The 3-4MB taste file is not just a model checkpoint. It's a compressed representation of a person's aesthetic, at a moment in time, portable enough to fit in a text message.

---

*All code referenced in this article is from the on-device transformer implementation in `lib/helpers/transformer_model.dart`, `lib/helpers/isolate_trainer.dart`, and `lib/helpers/transformer_manager_with_isolate.dart`.*
