<div align="center">

![NeuralKeep banner](assets/neuralkeep-banner.svg)

# 🧠 NeuralKeep

**A private home for the things worth remembering.**

*Your knowledge. Your hardware. Your intelligence.*

[![Status](https://img.shields.io/badge/status-experimental-ffbd69?style=for-the-badge)](#project-status)
[![Self-hosted](https://img.shields.io/badge/self--hosted-yes-43d9bd?style=for-the-badge)](#architecture)
[![Local AI](https://img.shields.io/badge/local_AI-in_progress-83a8ff?style=for-the-badge)](#local-ai)
[![Platform](https://img.shields.io/badge/platform-Apple_Silicon-222c3a?style=for-the-badge)](#tech-stack)

**[Explore the architecture](#architecture) · [Get started](#getting-started) · [AI integration](docs/ai-integration.md) · [Roadmap](#roadmap)**

</div>

---

## ✨ The idea

**Your bookmarks deserve better than a forgotten folder.**

NeuralKeep is a self-hosted knowledge and media library built around **Karakeep**, with private access from your devices and an experimental connection to a **local Qwen language model**.

Save interesting articles, videos and links. Search them later. Explore automatic organisation without sending your personal library to a third-party AI service.

> [!IMPORTANT]
> **NeuralKeep is an integration and documentation project — not a Karakeep fork or a finished product.** Local AI tagging is being configured and has **not yet been verified end-to-end**.

## ⚡ At a glance

| 📥 Collect | 🔎 Rediscover | 🔒 Stay private | 🧠 Think locally |
|:--|:--|:--|:--|
| Bookmarks, articles and videos | Search via Meilisearch | Access over a private Tailscale network | Experimental Qwen inference with MLX |
| **Working** | **Working** | **Working** | **In progress** |

## 🧩 Architecture

```mermaid
flowchart LR
    A["📱 iPhone / Browser"] -->|"Private tailnet"| B["🔐 Tailscale Serve"]
    B --> C["🗂️ Karakeep"]
    subgraph D["Docker"]
      C --> E["🌐 Chromium crawler"]
      C --> F["🔎 Meilisearch"]
    end
    C -. "OpenAI-compatible API" .-> G["⚡ MLX on macOS"]
    G -.-> H["🧠 Local Qwen"]
    classDef ai fill:#1a3c45,stroke:#54eacb,color:#e6fffa
    class G,H ai
```

**Solid arrows:** deployed application flow. **Dotted arrows:** experimental local AI integration.

## 🛠️ Tech stack

| Layer | Technology | Role |
|---|---|---|
| Application | [Karakeep](https://github.com/karakeep-app/karakeep) | Bookmark and media library |
| Containers | Docker Compose | Reproducible service deployment |
| Capture | Chromium | Website crawling and previews |
| Search | Meilisearch | Finding saved content |
| Private access | Tailscale Serve | Secure access from mobile devices |
| Local inference | MLX + Qwen | Experimental AI-assisted organisation |
| Host | macOS / Apple Silicon | Runs the application and inference server |

## 🚀 Getting started

This repository currently documents the reference setup rather than offering a verified one-command installer.

**01 — Deploy Karakeep.** Follow the [deployment guide](docs/setup.md) and the [upstream documentation](https://docs.karakeep.app/).

**02 — Connect your devices.** Use [Tailscale Serve](docs/remote-access.md) for private mobile access.

**03 — Experiment with local AI.** See the [MLX/Qwen integration notes](docs/ai-integration.md). Test real completions before enabling bulk tagging.

**04 — Keep your data yours.** Store secrets locally and keep personal bookmarks, databases and exports out of Git.

## 🧠 Local AI

The goal is simple: **save a link, let your own hardware help make sense of it.**

```text
Saved bookmark
      │
      ▼
  Karakeep
      │
      ▼
Local API endpoint ──► MLX ──► Qwen
      │
      ▼
  Suggested tags  (not yet validated)
```

The reference environment can reach the MLX API from Docker. **Model listing and connectivity are not proof of successful tagging.** Chat-completion compatibility and Karakeep integration still need testing.

## 🗺️ Roadmap

- [x] Deploy Karakeep using Docker
- [x] Enable private iPhone access via Tailscale
- [x] Verify container-to-host MLX API connectivity
- [ ] Validate a real Qwen chat-completion request
- [ ] Verify automatic tagging end-to-end
- [ ] Publish a clean, tested Compose example
- [ ] Document backup and restore
- [ ] Explore semantic search and richer local knowledge workflows

## 🔐 Privacy first

No real credentials, private hostnames, personal IP addresses, bookmarks, databases or Docker volumes belong in this repository. Keep your real `.env` private; use [`.env.example`](.env.example) as a starting point.

Read [SECURITY.md](SECURITY.md) before exposing services or sharing logs.

## 📡 Project status

**v0.1 · Experimental / actively documented**

NeuralKeep brings existing open-source tools together. It is an independent community project and is **not affiliated with or endorsed by Karakeep**.

### Licensing and upstream software

**NeuralKeep's original documentation, configuration examples and other original files in this repository are MIT-licensed** (see [LICENSE](LICENSE)).

**Karakeep is a separate upstream project licensed under the GNU Affero General Public License v3.0 (AGPL-3.0).** NeuralKeep does not vendor, redistribute or modify Karakeep's source code; users obtain it separately from [the official Karakeep repository](https://github.com/karakeep-app/karakeep) or its published images. Karakeep's AGPL-3.0 terms continue to apply to Karakeep independently of NeuralKeep's MIT licence.

Other dependencies retain their own licences. If this repository later distributes upstream code or derivative works, the licensing implications will need to be reassessed.

---

<div align="center">

### Built for curious minds who prefer to own their tools.

**Collect more. Lose less. Keep control.** 🧠

</div>
