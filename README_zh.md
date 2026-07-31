<div align="center">

<img src="https://github.com/user-attachments/assets/79d40b15-6e03-4d68-878f-3e60e21f7df6" alt="Uhella — 释放你的 iCloud 空间" width="420" />

# Uhella

### 不再为 iCloud 储存付费 — 每一张照片，都留在你自己的 Mac 上。

[![下载](https://img.shields.io/github/v/release/paulxiong/uhella?label=%E4%B8%8B%E8%BD%BD&color=0A84FF)](https://github.com/paulxiong/uhella/releases/latest)
![macOS 13+](https://img.shields.io/badge/macOS-13%2B-black?logo=apple)
![Apple Silicon 和 Intel](https://img.shields.io/badge/Mac-Apple%20Silicon%20%7C%20Intel-555)
![100%25 本地 AI](https://img.shields.io/badge/AI-100%25%20%E6%9C%AC%E5%9C%B0-34C759)
![免费](https://img.shields.io/badge/%E4%BB%B7%E6%A0%BC-%E5%85%8D%E8%B4%B9-34C759)

**[⬇️ 下载 Mac 版](https://github.com/paulxiong/uhella/releases/latest)** &nbsp;·&nbsp; **[▶️ 观看演示](https://www.youtube.com/watch?v=K22UKcL3UnM)** &nbsp;·&nbsp; **[English](README.md)**

</div>

---

## Uhella 是什么？

Uhella 是一款免费的 Mac 菜单栏应用，专治「iCloud 储存空间已满」——而且不用删掉任何一张回忆。

它会把**较早的照片原件安全地从 iCloud 移到你自己的 Mac 或外接硬盘上**，并且只有在你的磁盘上已经存在一份**校验过的副本**之后，才会从 iCloud 移除。除此之外，它还提供**私密的 AI 照片搜索**：在手机上问一句「狮子」「2025 年内罗毕」「海边的日落」，回答你的 AI 完全运行在你自己的 Mac 上。

手机端可以用免费的 **[Uhella iPhone App](https://apps.apple.com/us/app/uhella/id6737429973)**（它会在 Wi-Fi 里自动找到你的 Mac），也可以用 Telegram 机器人，随你喜欢。

## 为什么选 Uhella？

|  | 云端照片服务 | **Uhella** |
|---|---|---|
| 照片会上传到别人的服务器 | 会 | **绝不 — 一直在你的 Mac 上** |
| 没有网络也能 AI 搜索 | 不能 | **能 — 100% 本地 AI** |
| 月费 | 每月 $3–$12，年年交 | **免费** |
| 储存设备归谁 | 他们 | **你自己** |

iCloud 2 TB 每年约 **$120**。而你手边多半已经有一块硬盘，能永久免费装下同样的照片。Uhella 不是要取代 iCloud 或 Google Photos，而是叠加在你现有方案之上的伙伴，帮你把账单降下来。

## 工作原理

1. **本地 AI 认识你的照片库。** 视觉模型在你的 Mac 上逐张「看」照片并写下描述，任何内容都不会上传。
2. **原件归档到你的硬盘。** 全画质原件持续导出到你指定磁盘上的按月文件夹（`~/uhella_archives/2026-07/ …`），每个文件都会**校验**。
3. **用你喜欢的方式释放 iCloud 空间：**
   - Uhella 通知你「照片已安全归档」时，点一下 **Move** 按钮；或
   - 设置 **Auto Archive**（「iCloud 里最多保留 N 张」），让它全自动运行；或
   - 把照片拖进 **Move Now** 相簿，立即归档。

> **安全第一 🛡️**
> 在你的硬盘上存在校验过的副本之前，任何照片都不会从 iCloud 移除。**Keep Forever** 相簿里的照片永远不会被动。所有已归档的照片都可以**恢复**回「照片」App，并保留原始拍摄日期。

## 功能一览

- 🔍 **按记忆搜索，而不是按关键词** —「去年夏天孩子们在海边」直接就能搜到，本地 Gemma + 视觉模型驱动
- 📱 **自带 iPhone App** — 免费的 [Uhella App](https://apps.apple.com/us/app/uhella/id6737429973) 直连你的 Mac，家里 Wi-Fi 下零配置
- 💬 **Telegram 机器人（可选）** — 喜欢聊天式操作？在任何 Telegram 客户端里搜索、浏览、释放空间
- 🗄️ **Auto Archive** — 把 iCloud 固定在某个规模，最早的照片自动移出
- ⚡ **Move Now / Keep Forever 相簿** — 两个拖拽相簿，完全手动掌控
- 📚 **多照片图库** — 菜单栏里随时切换
- 💾 **任意硬盘，随时搬家** — 归档到外接磁盘，之后用「Copy Storage to New Drive」整体迁移
- ♻️ **随时恢复** — 归档照片可恢复回「照片」App，日期不变
- 🔄 **自动更新** — 每 6 小时检查一次，自动升级

## 安装（约 10 分钟，大部分时间在等下载）

**你需要：** 一台 **macOS 13+** 的 Mac（Apple Silicon 或 Intel），约 8 GB 空闲磁盘放 AI 模型，最好是一台常开的 Mac —— **Mac mini** 最合适。

1. **[下载 DMG](https://github.com/paulxiong/uhella/releases/latest)** — Apple Silicon（M 系列）选 `…_arm64.dmg`，Intel Mac 选 `…_x86_64.dmg`。
2. 打开后把 **Uhella Backend 拖进「应用程序」**，然后启动。
3. **分步设置向导**会接管一切：自动下载本地 AI 模型（约 7 GB，仅一次，带进度条）。不用终端，不用配置文件。
4. macOS 询问时，允许**照片访问**、**完全磁盘访问**和**辅助功能**权限 — Uhella 会告诉你每一步点哪里。

装好之后，它会自己保持最新，iPhone App 在家里 Wi-Fi 下也已经可以直接使用，不用额外设置。

> **远程访问（见下文）完全可选。** 不需要的话可以跳过 —— 不设置远程访问，Uhella 在家里一样能完整使用，**无论你是否设置远程访问，Auto Archive 以及 Mac 上的其它一切都会照常运行。** 只有当你想在出门在外时也用 iPhone App 访问照片，才需要设置它。

## 日常使用

Uhella 常驻**菜单栏**：

<img src="menu.png" alt="Uhella 菜单栏" width="360" />

- **Auto Archive** — 设置「最多保留 N 张」上限
- **Photo Library** — 切换图库、重建索引、清理缓存
- **Telegram Bot / Configure Telegram** — 配置手机访问
- **Check for Updates**、**Start at Login**、**Keep Awake**

### 手机端

**在家里，和 Mac 连同一个 Wi-Fi 时，[Uhella iPhone App](https://apps.apple.com/us/app/uhella/id6737429973) 直接就能用** — 打开它就会**自动找到你的 Mac**，什么都不用配置。（也可以手动输入一次 Mac 的名称或 IP。）

想在**出门在外**时也能访问照片？这就是远程访问要解决的问题。从下面任选一种 —— 或者都不用，只在家里用 Uhella 也完全没问题：

**① Tailscale — 完整 App，随处可用。** 在 iPhone 和 Mac 上都安装免费的 **[Tailscale](https://tailscale.com)**，用同一个账号登录 — 设置就这么多。之后 Uhella App 会从任何地方安全地连回你的 Mac，不只是在家里。

完整教程：**[Tailscale 设置指南](uhella_tailscale_guide.md)**

**② Telegram — 聊天式访问，随处可用**（喜欢聊天式操作的话，约 4 分钟）：用 **@BotFather** 创建机器人，把 token 粘贴进菜单栏 App 里的 *Configure Telegram…*，就可以开聊：

> `狮子` · `Nairobi 2025` · `马达加斯加一共有多少张照片？` · `more` · `similar`

完整教程：**[Telegram 设置指南](uhella_telegram_guide.md)** · 演示视频：**[YouTube](https://www.youtube.com/watch?v=K22UKcL3UnM)**

**③ 静态 IP / 本地地址 — 手动兜底方式，仅限家里 Wi-Fi。** 在菜单栏 App 中打开 **Remote Access → Local Network (Static IP)…**，可以看到 Mac 的名称和地址；如果自动发现没找到，就手动输入到 iPhone App 的服务器选择器里。

完整教程：**[静态 IP 设置指南](uhella_static_ip_guide.md)**

## 常见问题

<details>
<summary><b>安全吗？会不会丢照片？</b></summary>

只有当照片原件已导出到你的硬盘**并通过校验**之后，它才有资格从 iCloud 移除。**Keep Forever** 相簿里的照片永远不会被移除。所有归档的照片都能恢复回「照片」App，拍摄日期保持不变。
</details>

<details>
<summary><b>哪些数据会离开我的 Mac？</b></summary>

你的照片**绝不会上传到任何 AI 或云服务** — 所有模型都在本地运行。只有当*你自己*通过你配置的 Telegram 机器人发送照片时，照片才会离开你的 Mac。（解析地名时，Uhella 只会向 OpenStreetMap 发送 GPS 坐标，绝不发送照片。）
</details>

<details>
<summary><b>必须用 Telegram 吗？</b></summary>

不用。**Uhella iPhone App** 直接和你的 Mac 通信 — 家里 Wi-Fi 下零配置连接。Telegram 只是给喜欢聊天式操作的人的另一种选择。
</details>

<details>
<summary><b>要花钱吗？</b></summary>

不用。应用免费，AI 模型免费且跑在你自己的硬件上。没有账号，没有订阅。
</details>

<details>
<summary><b>以后能把归档搬到更大的硬盘吗？</b></summary>

可以 — 菜单栏里的 *Copy Storage to New Drive…* 会整体迁移，归档硬盘随时可以插拔。
</details>

---

<div align="center">

**下载 Uhella App** — 在手机上搜索你的照片库，享受 AI 照片发现流：
[iOS / iPadOS](https://apps.apple.com/us/app/uhella/id6737429973) · [macOS](https://apps.apple.com/my/app/uhella/id6737429973?platform=mac)

有问题？**[提一个 Issue](https://github.com/paulxiong/uhella/issues)**

</div>
