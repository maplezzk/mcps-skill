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

## 配置示例

### 添加各种类型的 MCP 服务器

```bash
# 添加 fetch 服务器（网页抓取）
mcps add fetch --command uvx --args mcp-server-fetch

# 添加 PostgreSQL 服务器
mcps add postgres --command npx --args @modelcontextprotocol/server-postgres --env POSTGRES_CONNECTION_STRING="${DATABASE_URL}"

# 添加 GitLab 服务器
mcps add gitlab --command npx --args gitlab-mcp-server

# 添加 SSE 服务器
mcps add remote --type sse --url http://localhost:8000/sse

# 添加 HTTP 服务器
mcps add http-server --type http --url http://localhost:8000/mcp
```

### 配置文件示例 (~/.mcps/mcp.json)

```json
{
  "servers": [
    {
      "name": "fetch",
      "type": "stdio",
      "command": "uvx",
      "args": ["mcp-server-fetch"]
    },
    {
      "name": "postgres",
      "type": "stdio",
      "command": "npx",
      "args": ["@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "${DATABASE_URL}"
      }
    },
    {
      "name": "gitlab",
      "type": "stdio",
      "command": "npx",
      "args": ["gitlab-mcp-server"],
      "env": {
        "GITLAB_PERSONAL_ACCESS_TOKEN": "${GITLAB_TOKEN}",
        "GITLAB_API_URL": "https://gitlab.com/api/v4"
      }
    }
  ]
}
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

## 实际使用示例

### 场景 1: 网页抓取和搜索

```bash
# 抓取网页内容
mcps call fetch fetch url="https://example.com" max_length=5000

# 深度抓取（跟踪链接）
mcps call fetch fetch url="https://example.com" follow_redirects=true max_depth=2

# 带筛选条件的抓取
mcps call fetch fetch url="https://news.example.com" include_tags='["article", "p"]' exclude_tags='["script", "style"]'
```

### 场景 2: 数据库查询

```bash
# 先查看表结构
mcps tools postgres | grep describe

# 查询数据（自动解析参数类型）
mcps call postgres query sql="SELECT * FROM users WHERE active = true LIMIT 10"

# 字符串参数保持原样（用 --raw）
mcps call postgres query --raw sql="SELECT * FROM orders WHERE id = '12345'"
```

### 场景 3: 复杂参数传递

```bash
# JSON 对象参数
mcps call my-server createUser user='{"name": "Alice", "age": 30, "tags": ["admin", "user"]}'

# 从文件加载 JSON
mcps call my-server createUser --json user.json

# 混合参数（部分自动解析，部分保持字符串）
mcps call my-server update --raw id="123" data='{"name": "Updated"}'
```

### 场景 4: 服务器管理

```bash
# 查看所有服务器配置
mcps ls

# 查看活跃连接状态
mcps status

# 重启单个服务器
mcps restart postgres

# 重启所有服务器
mcps restart

# 禁用服务器（不删除配置）
mcps update my-server --disabled true

# 删除服务器
mcps rm my-server
```

### 场景 5: 工具过滤和查找

```bash
# 只显示工具名称（简洁模式）
mcps tools postgres --simple

# 按关键词过滤工具
mcps tools postgres --tool query --tool describe

# 查找包含 "create" 的工具
mcps tools postgres --tool create
```

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

**Q: 参数中的特殊字符（如 SQL）怎么处理？**
```bash
# 使用 --raw 保持字符串格式
mcps call alibaba-dms createDataChangeOrder --raw \
  database_id="36005357" \
  script="DELETE FROM table WHERE id = 'xxx';" \
  logic="true"
```

**Q: 守护进程启动慢怎么办？**
- 首次启动需要加载所有服务器，10-15 秒是正常的
- 后续启动会快很多（~2 秒）
- 可以用 `mcps ls` 检查配置，无需启动守护进程
