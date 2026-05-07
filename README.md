# 🚀 CF-PERSONAL-API-GATEWAY

基于 Cloudflare Workers 打造的高性能、全能型 AI 大模型 API 路由网关。
利用 Cloudflare 边缘网络，将全球多家 AI 供应商（Cerebras, OpenRouter, Nvidia, 通义千问等）统一封装为标准的高效接口。

---

## 📂 版本说明 (Version Guide)

本项目目前包含两个主要版本，满足不同复杂度的使用需求：

### 1. 基础版: `workers.json` (V1)
**定位：** 轻量级、硬编码的前缀路由代理。
* **主要功能**：
  * 支持通过模型前缀（如 `cerebras/`, `openrouter/`, `nvidia/`）将请求分发给不同供应商。
  * 内置全局 `PROXY_KEY` 安全校验防盗刷。
  * 完善的跨域 (CORS) 支持与模拟 `/models` 接口，兼容各大主流 AI 客户端（如 Chatbox, LobeChat, Cherry Studio）。
* **适用场景**：不需要频繁切换模型，喜欢在客户端直接指定具体模型名称（如 `cerebras/llama3.1-70b`）的用户。

### 2. 旗舰版: `workers -V2.json` (V2) 🌟推荐🌟
**定位：** 企业级智能体路由调度中心 (Agent Routing Console)。
* **核心升级**：
  * **🎛️ KV 动态路由调度**：告别硬编码！引入虚拟角色（如 `role/coder`, `role/artist`），随时在云端切换底层真实模型，客户端零感知。
  * **🖥️ 中英双语 Web 控制台**：内置现代化网页管理后台，无需改代码即可在浏览器中一键切换所有智能体的默认模型。
  * **🔐 权限分离**：API 调用使用 `PROXY_KEY`，后台登录使用 `ADMIN_PASSWORD`，全面保障安全。
  * **🧹 智能消息净化器**：自动剥离跨模型调用时不兼容的参数（如 `reasoning_content`），彻底解决“思考型模型”切换到“普通模型”时的报错死机问题。
  * **🎨 文本降维画图黑魔法**：利用 Cloudflare原生算力，将二进制图像生成无缝伪装成 Markdown 文本返回。普通的纯文字聊天框也能直接“画”出图像！
  * **🛡️ HTTP-Referer 补全**：完美绕过部分提供商（如 OpenRouter）的来源风控限制。

---

## 🛠️ V2 旗舰版部署指南

### 第一步：创建 KV 数据库
1. 在 Cloudflare 面板进入 **Workers & Pages** -> **KV**。
2. 创建一个名为 `MODEL_ROUTING` 的命名空间。
3. 进入你的 Worker 设置 -> **Bindings (绑定)** -> 新增 KV 绑定：
   * 变量名称必须为：`KV_STORE`
   * 命名空间选择刚刚创建的 `MODEL_ROUTING`

### 第二步：配置环境变量 (Secrets)
在 Worker 的 **Settings -> Variables and Secrets** 中添加以下机密：

| 变量名 | 说明 |
| :--- | :--- |
| `PROXY_KEY` | **【必填】** API 调用密钥（建议使用 `sk-` 开头的复杂密码） |
| `ADMIN_PASSWORD`| **【强烈建议】** Web 后台登录密码。若不填，默认使用 `PROXY_KEY` |
| `CEREBRAS_KEY` | Cerebras 平台的 API Key |
| `OPENROUTER_KEY` | OpenRouter 平台的 API Key |
| `NVIDIA_KEY` | NVIDIA 接口密钥 |
| `CF_ACCOUNT_ID` | 你的 Cloudflare Account ID（画图及 CF 原生模型必填） |
| `CF_API_TOKEN` | 具备 Workers AI 读取权限的 Token |
| `BLAZE_KEY` | Blaze / Kilo / FreeTheAI 等其他提供商密钥 |

### 第三步：部署代码
将 `workers -V2.json` 中的全部代码复制并部署到你的 Cloudflare Worker 中。

---

## 🎮 使用说明

### 1. 登录路由管理后台
部署完成后，在浏览器访问：
`https://你的worker域名.workers.dev/admin?key=你的ADMIN_PASSWORD`
在此页面，你可以为你的日常助理、编程、画图等各个角色分配最合适的顶尖模型。

### 2. 在 AI 客户端中配置 (如 OpenClaw / Cherry Studio)
* **API 地址 (Base URL)**: `https://你的worker域名.workers.dev/v1`
* **API 密钥 (API Key)**: 填写你的 `PROXY_KEY`
* **模型名称 (Model)**: 
  * 填写 **`role/assistant`** (日常助手)
  * 填写 **`role/coder`** (写代码)
  * 填写 **`role/reasoner`** (长文本与推理)
  * 填写 **`role/artist`** (免费绘画 - Markdown 渲染)
  * *(只需配置这些虚拟名称，网关会自动在后台将它们路由到你最新选定的真实模型)*

## 📄 许可证
[MIT License](LICENSE)
