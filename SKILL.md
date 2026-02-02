---
name: mcps
description: MCP CLI Manager - 管理 MCP 服务器和调用工具
homepage: https://github.com/maplezzk/mcps
metadata: {"clawdbot":{"emoji":"🔌","requires":{"bins":["mcps"]},"install":[{"id":"npm","kind":"node","package":"@maplezzk/mcps","bins":["mcps"],"label":"Install mcps"}]}}
---

# mcps - MCP CLI Manager

一个强大的命令行工具，用于管理和调用 MCP (Model Context Protocol) 服务器。

## 安装

```bash
npm install -g @maplezzk/mcps
```

## 快速开始

```bash
# 1. 添加 MCP 服务器
mcps add fetch --command uvx --args mcp-server-fetch

# 2. 启动守护进程
mcps start

# 3. 查看状态
mcps status

# 4. 列出可用工具
mcps tools fetch

# 5. 调用工具
mcps call fetch fetch url="https://example.com"
```

## 命令速查

### 服务器管理

| 命令 | 说明 |
|------|------|
| `mcps ls` | 列出所有已配置服务 |
| `mcps add <name> --command <cmd> --args <args>` | 添加服务 |
| `mcps rm <name>` | 移除服务 |
| `mcps update [name]` | 更新服务器配置 |

### 守护进程

| 命令 | 说明 |
|------|------|
| `mcps start [--verbose]` | 启动守护进程 |
| `mcps stop` | 停止守护进程 |
| `mcps restart [server]` | 重启守护进程或特定服务 |
| `mcps status` | 查看守护进程状态 |

### 工具调用

| 命令 | 说明 |
|------|------|
| `mcps tools <server> [--simple]` | 查看服务工具列表 |
| `mcps call <server> <tool> [args...]` | 调用工具 |

## 调用工具的参数传递

### 默认模式（自动解析 JSON）

```bash
mcps call fetch fetch url="https://example.com"
mcps call fetch fetch max_length=5000 follow_redirects=true
```

### --raw 模式（保持字符串）

```bash
mcps call my-db createOrder --raw order_id="12345" sku="ABC-001"
```

### --json 模式（复杂参数）

```bash
mcps call my-server createUser --json '{"name": "Alice", "age": 30}'
mcps call my-server createUser --json params.json
```

## 配置

- **配置文件**: `~/.mcps/mcp.json`
- **环境变量**: 
  - `MCPS_CONFIG_DIR`: 配置目录
  - `MCPS_PORT`: 守护进程端口（默认 4100）
  - `MCPS_VERBOSE`: 详细日志模式

## 常见问题

**Q: 如何检查服务器状态？**
```bash
mcps status  # 检查活跃连接
mcps ls      # 检查所有配置（包括禁用的）
```

**Q: 服务器连接失败怎么办？**
```bash
mcps start --verbose  # 查看详细日志
mcps restart my-server  # 重启特定服务器
```

**Q: 如何快速查找工具？**
```bash
mcps tools my-server --tool keyword  # 按关键词过滤
mcps tools my-server --simple        # 只显示名称
```
