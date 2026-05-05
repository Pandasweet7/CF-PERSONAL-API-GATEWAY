# CF-PERSONAL-API-GATEWAY

基于 Cloudflare Workers 搭建的高性能、全能型 AI API 中转网关。

## 🌟 项目简介

本项目旨在通过 Cloudflare 的全球边缘网络，将多个不同平台的大模型 API（包括 Cerebras, OpenRouter, NVIDIA, Kilo 等）统一封装为标准的 OpenAI API 格式。

### 核心优势：
- **极致速度**：利用 Cloudflare 边缘计算，大幅降低跨境网络延迟，支持流式输出（Streaming）。
- **多平台集成**：一次部署，即可同时访问 Cerebras、OpenRouter、Cloudflare Workers AI 等多个供应商。
- **安全防盗刷**：内置全局 `PROXY_KEY` 校验，保护你的 API 额度不被非法使用。
- **零成本运维**：利用 Cloudflare Workers 免费额度，无需购买额外 VPS 服务器。

## 🚀 快速部署

### 1. 准备工作
- 拥有一个 [Cloudflare](https://www.cloudflare.com/) 账号。
- 获取各 AI 平台的 API Key。

### 2. 创建 Worker
1. 在 Cloudflare 控制台创建一个新的 **Worker**。
2. 将项目中的 `workers.js`（或相关逻辑代码）粘贴到编辑器中。

### 3. 配置环境变量
在 Worker 的 **Settings -> Variables** 中添加以下机密（Secrets）：

| 变量名 | 说明 |
| :--- | :--- |
| `PROXY_KEY` | **必填**。你自定义的全局访问密钥 (建议以 `sk-` 开头) |
| `CEREBRAS_KEY` | Cerebras.ai 的 API Key |
| `OPENROUTER_KEY` | OpenRouter 的 API Key |
| `CF_ACCOUNT_ID` | 你的 Cloudflare 账户 ID |
| `CF_API_TOKEN` | 具备 AI 访问权限的 Cloudflare Token |
| `NVIDIA_KEY` | NVIDIA 接口密钥 |
| `KILO_KEY` | Kilo API 密钥 |

## 🛠️ 使用说明

### 客户端配置
在任何支持 OpenAI 格式的客户端（如 NextChat, Chatbox, LobeChat）中配置：

- **接口地址 (Base URL)**: `https://你的域名` 或 `https://your-worker.workers.dev/v1`
- **API 密钥 (API Key)**: 填入你在环境变量中设置的 `PROXY_KEY`
- **模型名称 (Model)**: 使用 `前缀/真实模型名` 的方式调用。

### 路由前缀参考
| 前缀 | 目标平台 | 示例模型名 |
| :--- | :--- | :--- |
| `cerebras/` | Cerebras | `cerebras/llama3.1-70b` |
| `openrouter/` | OpenRouter | `openrouter/anthropic/claude-3.5-sonnet` |
| `@cf/` | CF Native | `@cf/meta/llama-3-8b-instruct` |
| `nvidia/` | NVIDIA | `nvidia/meta/llama-3.1-405b-instruct` |

## 🛡️ 安全提示
请勿将包含真实 API Key 的代码提交到公共仓库。本项目推荐使用 Cloudflare 环境变量功能来安全存储密钥。

## 📄 开源协议
[MIT License](LICENSE)