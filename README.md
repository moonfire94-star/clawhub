# OpenClaw Admin Console

> 🌐 [中文](#中文) | [English](#english)

---

## English

A browser-based admin panel for OpenClaw Gateway. Monitor multiple nodes, view real-time logs, chat with Agents, and get a bird's-eye view of all your nodes in the Lobster Tank. Single-file, pure frontend — no install required.

### Features

**Core**
- **Multi-node monitoring** — Add and manage multiple OpenClaw Gateway nodes
- **Real-time logs** — View gateway operation logs with level filtering and keyword search
- **Agent chat** — Talk directly to Agents on any connected node
- **🦞 Tank Overview** — Visual overview of all nodes; each node is a crawling lobster

**Status Dashboard**
- Connection status and uptime
- Agent session count and total agents
- Online client count (presence)
- Channel status (WeCom, etc.)
- Skills list and scheduled tasks

**Security**
- **Device pairing** — Ed25519 keypair signature authentication
- **WebSocket encryption** — WSS (TLS) support
- **Token auth** — Integrates with the `auth.token` field in your gateway config

**Technical**
- **Single-file app** — 2400+ lines in one `index.html`
- **Pure frontend** — HTML + CSS + JavaScript, no backend needed
- **Persistent storage** — `localStorage` for node config, `IndexedDB` for device identity
- **Real-time** — WebSocket connection to Gateway
- **Bilingual** — EN / 中文, switchable at runtime via the toggle in the sidebar

### Quick Start

#### 1. Start a local HTTP server

> ⚠️ **Must be served over HTTP** — do not open `index.html` directly via `file://`.  
> Device pairing uses the WebCrypto API, which browsers disable on `file://` origins.

**Python (recommended):**
```bash
cd /path/to/openclaw-console
python3 -m http.server 8080
```

**Node.js:**
```bash
npx serve .
```

Open `http://localhost:8080` in your browser.

---

### Get Node Info

**Find your Token:**
```bash
cat ~/.openclaw/openclaw.json | grep -A3 '"auth"'
```
```json
"auth": {
  "mode": "token",
  "token": "your-gateway-token-here"
}
```

**Find port and bind mode:**
```bash
cat ~/.openclaw/openclaw.json | grep -E '"port"|"bind"'
```

| `bind` value | Meaning | SSH tunnel needed? |
|---|---|---|
| `loopback` | Only listens on 127.0.0.1 | **Yes** |
| `lan` | Listens on LAN IP | No — use LAN IP directly |
| `tailnet` | Tailscale network | No — use Tailscale IP |

---

### SSH Tunnel (required when `bind: loopback`)

```bash
# Forward remote port 18789 to local 19001
ssh -L 19001:127.0.0.1:18789 user@remote-host
```

Keep this terminal open — closing it drops the tunnel.

**Multiple machines:**
```bash
ssh -L 19001:127.0.0.1:18789 user@host-A
ssh -L 19002:127.0.0.1:18789 user@host-B
```

**Simplify with `~/.ssh/config`:**
```
Host openclaw-A
  HostName 192.168.1.10
  User ubuntu
  LocalForward 19001 127.0.0.1:18789
```

Then `ssh openclaw-A` opens the tunnel automatically.

**Verify:**
```bash
curl http://localhost:19001/
```

---

### Add a Node

Click **＋ Add Node** in the top-right or the sidebar button.

| Field | Description | Example |
|---|---|---|
| Node Name | Display label | `Production Server` |
| Host | IP or hostname | `localhost` |
| Port | Gateway listen port | `18789` / `19001` (SSH tunnel) |
| WebSocket Path | Fill if `basePath` is set | `/dqnpg2` |
| Auth Mode | Matches `auth.mode` in config | `Token` |
| Token | Value of `auth.token` | `your-token...` |
| Use WSS | Enable only for TLS | No |

---

### Device Pairing

First connection from a new browser requires one-time approval.

**You will see:** `Policy violation (Code 1008)`

**Run on the server:**
```bash
openclaw devices list
openclaw devices approve <requestId>
```

> Local connections via SSH tunnel **pair automatically**.

---

### FAQ

**WebSocket error on connect** — Check SSH tunnel is running, port is correct, and WebSocket path matches.

**Skills / Tasks show "Waiting for data…"** — Click Refresh or reconnect. Check device scopes include `operator.read`.

**Chat has no response** — Run `openclaw gateway call health` and check the `agents` array.

**Re-pair after browser restart?** — No. Identity persists in `IndexedDB` until you clear browser data.

---

## 中文

基于浏览器的 OpenClaw Gateway 管理面板，支持多节点监控、实时日志、Agent 对话和龙虾围栏总览。单文件纯前端实现，无需安装，打开即用。

### 主要功能

**核心功能**
- **多节点监控** — 添加和管理多个 OpenClaw Gateway 节点
- **实时日志** — 查看 Gateway 操作日志，支持级别过滤和关键词搜索
- **Agent 对话** — 直接与节点上的 Agent 进行交互对话
- **🦞 围栏总览** — 可视化展示所有节点状态，每个节点用一只龙虾表示

**状态总览**
- 连接状态和运行时长
- Agent 会话数和数量统计
- 在线客户端数
- 渠道状态（企业微信等）
- 技能清单和定时任务

**安全特性**
- **设备配对机制** — 基于 Ed25519 密钥对签名认证
- **WebSocket 加密** — 支持 WSS 安全连接
- **Token 认证** — 与 Gateway 配置文件的 `auth.token` 对接

**技术实现**
- **单文件应用** — 2400+ 行代码全部在一个 `index.html` 中
- **纯前端实现** — HTML + CSS + JavaScript，无需后端
- **数据持久化** — `localStorage` 存储节点配置，`IndexedDB` 存储设备身份密钥
- **实时通信** — WebSocket 连接 Gateway
- **双语界面** — 支持 EN / 中文，侧边栏切换按钮实时切换

### 快速开始

#### 1. 启动本地 HTTP 服务器

> ⚠️ **必须通过 HTTP 服务器访问**，不能直接双击打开 `index.html`。  
> 原因：设备配对需要 WebCrypto API，浏览器在 `file://` 协议下会禁用此功能。

**Python（推荐）：**
```bash
cd /path/to/openclaw-console
python3 -m http.server 8080
```

**Node.js：**
```bash
npx serve .
```

然后在浏览器打开：`http://localhost:8080`

---

### 获取节点信息

**查看 Token：**
```bash
cat ~/.openclaw/openclaw.json | grep -A3 '"auth"'
```
```json
"auth": {
  "mode": "token",
  "token": "your-gateway-token-here"
}
```

**查看端口和绑定模式：**
```bash
cat ~/.openclaw/openclaw.json | grep -E '"port"|"bind"'
```

| `bind` 值 | 含义 | 是否需要 SSH 隧道 |
|---|---|---|
| `loopback` | 只监听本机 127.0.0.1 | **需要** |
| `lan` | 监听局域网 IP | 不需要，直接填局域网 IP |
| `tailnet` | Tailscale 网络 | 不需要，填 Tailscale IP |

---

### SSH 隧道（bind: loopback 时必须）

```bash
# 将远程机器的 18789 端口映射到本地 19001
ssh -L 19001:127.0.0.1:18789 user@远程机器IP
```

**此窗口必须保持开着**，关掉后隧道断开。

**管理多台机器：**
```bash
ssh -L 19001:127.0.0.1:18789 user@机器A
ssh -L 19002:127.0.0.1:18789 user@机器B
```

**用 SSH config 简化：**
```
Host openclaw-A
  HostName 192.168.1.10
  User ubuntu
  LocalForward 19001 127.0.0.1:18789
```

之后直接 `ssh openclaw-A` 即可自动建隧道。

**验证隧道：**
```bash
curl http://localhost:19001/
```

---

### 配置节点

在管理面板右上角点击「**＋ 添加节点**」，或侧边栏底部的添加按钮。

| 字段 | 说明 | 示例 |
|---|---|---|
| 节点名称 | 自定义标识，仅用于展示 | `生产服务器` |
| 主机地址 | IP 或域名 | `localhost` |
| 端口 | gateway 监听端口 | `18789` / `19001`（SSH 隧道） |
| WebSocket 路径 | 若配置了 `basePath` 填写，否则留空 | `/dqnpg2` |
| 认证方式 | 对应配置文件中的 `auth.mode` | `Token` |
| Token | 配置文件中的 `auth.token` 值 | `your-token...` |
| 使用 WSS | 仅 HTTPS/TLS 环境需要开启 | 否 |

---

### 设备配对

首次从新浏览器连接时，Gateway 需要一次性审批。

**你会看到：** `策略违规 (Code 1008)` 或 `设备需要配对`

**在服务器上执行：**
```bash
openclaw devices list
openclaw devices approve <requestId>
```

> 本地连接（127.0.0.1）通过 SSH 隧道访问时会**自动配对**，无需手动审批。

---

### 常见问题

**连接一直显示「WebSocket 发生错误」** — 检查 SSH 隧道是否在运行、端口是否正确、WebSocket 路径是否匹配。

**技能清单 / 定时任务显示「等待数据」** — 点击刷新或断开重连。检查设备 scopes 是否包含 `operator.read`。

**对话没有任何响应** — 运行 `openclaw gateway call health`，查看 `agents` 数组中是否有活跃 agent。

**重启浏览器后需要重新配对吗？** — 不需要。设备身份存储在 `IndexedDB`，清除浏览器数据才需要重新配对。

---

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

```
Apache License 2.0

Copyright (c) 2026 OpenClaw Contributors

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
