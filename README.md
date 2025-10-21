<div align="center">
  <a href="https://github.com/poixeai/proxify">
    <img src="./web/public/x.svg" alt="Proxify Logo" width="100" height="100">
  </a>
  <h1>Proxify</h1>
  <p>An open-source, lightweight, and self-hosted reverse proxy gateway for AI APIs</p>

English / [简体中文](./README_CN.md)

  <p>
    <strong>Supports nearly all major AI model providers including OpenAI, Anthropic (Claude), Google (Gemini), and DeepSeek</strong>
  </p>
  <p>
    <a href="https://github.com/poixeai/proxify/blob/main/LICENSE">
      <img alt="License" src="https://img.shields.io/github/license/poixeai/proxify?style=for-the-badge&color=blue">
    </a>
    <a href="https://github.com/poixeai/proxify">
      <img alt="Go Version" src="https://img.shields.io/github/go-mod/go-version/poixeai/proxify?style=for-the-badge">
    </a>
    <a href="https://github.com/poixeai/proxify/stargazers">
      <img alt="Stars" src="https://img.shields.io/github/stars/poixeai/proxify?style=for-the-badge&logo=github">
    </a>
    <a href="https://github.com/poixeai/proxify/issues">
      <img alt="Issues" src="https://img.shields.io/github/issues/poixeai/proxify?style=for-the-badge&logo=github">
    </a>
  </p>

  <h4>
    <a href="https://proxify.poixe.com">Live Demo</a>
    <span> · </span>
    <a href="#-quick-start">Quick Start</a>
    <span> · </span>
    <a href="#-deployment-guide">Deployment Guide</a>
    <span> · </span>
    <a href="#-supported-endpoints">Supported Endpoints</a>
  </h4>

  <img src="./assets/images/home_en_bg.png" alt="Proxify Logo">
</div>

---

**Proxify** is a high-performance reverse proxy gateway written in Go.
It allows developers to access various large model APIs through a unified entry point — solving problems such as regional restrictions and multi-service configuration complexity.
Proxify is deeply optimized for LLM streaming responses, ensuring the best performance and smooth user experience.

## ✨ Features

* 💎 **Powerful Extensibility**:
  More than just an AI gateway — Proxify is a universal reverse proxy server with special optimizations for LLM APIs, including stream smoothing, heartbeat keepalive, and tail acceleration.

* 🚀 **Unified API Entry**:
  Route to multiple upstreams through a single-level path — e.g., `/openai` → `api.openai.com`, `/gemini` → `generativelanguage.googleapis.com`.
  All routes are defined in one configuration file for simplicity and efficiency.

* ⚡ **Lightweight & High Performance**:
  Built with Golang and natively supports high concurrency with minimal memory usage. Runs smoothly on servers with as little as 0.5 GB RAM.

* 🚄 **Stream Optimization**:

  * **Smooth Output**: Built-in flow controller ensures a "typing effect" by streaming model responses smoothly.
  * **Heartbeat Keepalive**: Automatically inserts heartbeat messages into SSE (Server-Sent Events) streams to prevent idle timeouts.
  * **Tail Acceleration**: Keeps latency under control by accelerating the final part of the response.

* 🛡️ **Security & Privacy**:
  Fully self-hosted — all requests and data remain under your control. No third-party servers are involved, ensuring zero privacy risk.

* 🌐 **Broad Compatibility**:
  Preconfigured routes for major AI providers like OpenAI, Azure, Claude, Gemini, and DeepSeek. Easily extendable to any HTTP API via configuration.

* 🔧 **Easy Integration**:
  Switch from your existing API service to Proxify simply by updating the `BaseURL` — no code changes or request parameter modifications required.

* 👨‍💻 **Open Source & Professional**:
  Designed and maintained by a young and experienced AI engineering team. 100% open-source, auditable, and community-driven (PRs and Issues are welcome).

## 🛠️ Tech Stack

* **Backend Gateway**: Golang + Gin
* **Frontend Dashboard**: React + Vite + TypeScript + Tailwind CSS

## 🚀 Quick Start <a id="-quick-start"></a>

Integrating your existing services with Proxify only takes three steps.

#### 1. Identify Target Service

Browse the [Supported API list](#-supported-endpoints) and find the proxy path prefix (Path) for the desired service.

#### 2. Replace the Base URL

Replace the original API base URL in your code with your Proxify deployment address, appending the route prefix.

* **Original**:
  `https://api.openai.com/v1/chat/completions`
* **Replaced with**:
  `http://<your-proxify-domain>/openai/v1/chat/completions`

#### 3. Send Requests

Done! Use your existing API key and parameters as usual.
Your headers and request body remain unchanged.

#### Example (Node.js - OpenAI SDK)

```javascript
import OpenAI from "openai";

const openai = new OpenAI({
  apiKey: "sk-...", // your OpenAI API key
  baseURL: "http://127.0.0.1:7777/openai/v1", // your Proxify address
});

async function main() {
  const stream = await openai.chat.completions.create({
    model: "gpt-5",
    messages: [{ role: "user", content: "hi" }],
    stream: true,
  });
  for await (const chunk of stream) {
    process.stdout.write(chunk.choices[0]?.delta?.content || "");
  }
}
main();
```

## 🖥️ Deployment Guide <a id="-deployment-guide"></a>

Proxify offers multiple deployment options.
Before starting, make sure you’ve completed the setup steps below.

---

### ⚙️ Step 1: Configure Environment & Routes

Proxify includes `.env.example` and `routes.json.example`.
Copy and adjust them to your needs.

#### **1. Environment Variables (`.env`)**

```bash
cp .env.example .env
```

Example:

```env
# Mode: debug | release
MODE=debug

# Server port
PORT=7777

# Optional GitHub token
GITHUB_TOKEN=ghp_xxxx

# Stream optimization
STREAM_SMOOTHING_ENABLED=true
STREAM_HEARTBEAT_ENABLED=true
```

> 💡 **Tips:**
>
> * For Docker, mount `.env` into `/app/.env` inside the container.
> * For local binary, keep `.env` in the same directory as the executable.

---

#### **2. Route Configuration (`routes.json`)**

```bash
cp routes.json.example routes.json
```

Example:

```json
{
  "routes": [
    {
      "name": "OpenAI",
      "description": "OpenAI Official API Endpoint",
      "path": "/openai",
      "target": "https://api.openai.com/"
    },
    {
      "name": "DeepSeek",
      "description": "DeepSeek Official API Endpoint",
      "path": "/deepseek",
      "target": "https://api.deepseek.com"
    },
    {
      "name": "Claude",
      "description": "Anthropic Claude Official API Endpoint",
      "path": "/claude",
      "target": "https://api.anthropic.com"
    },
    {
      "name": "Gemini",
      "description": "Google Gemini Official API Endpoint",
      "path": "/gemini",
      "target": "https://generativelanguage.googleapis.com"
    }
  ]
}
```

> 💡 Routes can be modified freely — changes are automatically hot-reloaded without restarting the service.

---

### 🐳 Option 1: Deploy via Docker (Recommended)

#### 1. Pull from Docker Hub

```bash
docker pull poixeai/proxify:latest

docker run -d \
  --name proxify \
  -p 7777:7777 \
  -v $(pwd)/routes.json:/app/routes.json \
  -v $(pwd)/.env:/app/.env \
  --restart=always \
  poixeai/proxify:latest
```

#### 2. Deploy via Docker Compose

```bash
docker-compose up -d
docker-compose ps
```

#### 3. Build from Source

```bash
git clone https://github.com/poixeai/proxify.git
cd proxify
docker build -t poixeai/proxify:latest .
docker run -d -p 7777:7777 -v $(pwd)/routes.json:/app/routes.json -v $(pwd)/.env:/app/.env poixeai/proxify:latest
```

---

### 🛠️ Option 2: Manual Build

#### Requirements

* Go ≥ 1.20
* Node.js ≥ 18
* pnpm

#### 1. Use Build Script

```bash
git clone https://github.com/poixeai/proxify.git
cd proxify
chmod +x build.sh
./build.sh
./bin/proxify
```

#### 2. Manual Build Steps

```bash
cd web
pnpm install
pnpm build
cd ..
go mod tidy
go build -o ./bin/proxify .
./bin/proxify
```

---

## 🗺️ Supported Endpoints <a id="-supported-endpoints"></a>

Proxify can proxy **any HTTP service**.
Below are the preconfigured and optimized AI API routes:

| Provider       | Path          | Target URL                                  |
| -------------- | ------------- | ------------------------------------------- |
| **OpenAI**     | `/openai`     | `https://api.openai.com`                    |
| **Azure**      | `/azure`      | `https://<your-res>.openai.azure.com`       |
| **DeepSeek**   | `/deepseek`   | `https://api.deepseek.com`                  |
| **Claude**     | `/claude`     | `https://api.anthropic.com`                 |
| **Gemini**     | `/gemini`     | `https://generativelanguage.googleapis.com` |
| **Grok**       | `/grok`       | `https://api.x.ai`                          |
| **Aliyun**     | `/aliyun`     | `https://dashscope.aliyuncs.com`            |
| **VolcEngine** | `/volcengine` | `https://ark.cn-beijing.volces.com`         |

*⚠️ Actual available routes depend on your `routes.json` configuration.*

### 🔍 View Live Demo Routes

```bash
GET https://proxify.poixe.com/api/routes
```

👉 [View Current Demo Routes](https://proxify.poixe.com/api/routes)

---

## 🤝 Contributing

We welcome all contributions — whether it’s filing an issue, submitting a PR, or improving documentation.
Your support helps the community grow.

## 📄 License

This project is licensed under the [MIT License](./LICENSE).