# Personal AI Brain

基于 Garry Tan 的 Thin Harness + Fat Skills 架构，搭建的个人多 Agent 知识管理系统。

## 架构

```
Obsidian (写作源)
    │
    ▼
gbrain (知识库引擎) ── PGLite + BGE-M3 Embedding
    │
    ├── MCP Server (stdio transport)
    │       │
    │       ├── Claude Code ── Skills 系统
    │       │
    │       └── Hermes Agent ── 插件系统
    │
    └── SiliconFlow API (免费 embedding)
```

## 组件

| 组件 | 版本 | 作用 |
|------|------|------|
| **gbrain** | 0.33.2 | PGLite 知识库引擎，混合搜索（关键字 + 语义 + RRF 融合） |
| **Claude Code** | latest | 主交互层，Skill 技能分发 |
| **Hermes Agent** | 0.13.0 | Python Agent 编排层，MCP 客户端 |
| **SiliconFlow** | - | 免费 Embedding（BAAI/bge-m3, 1024维） |
| **DeepSeek V4** | - | 主推理模型（通过 api.deepseek.com/anthropic） |
| **Obsidian** | - | 写作源，Markdown 管理 |

## 检索系统

- **关键字搜索**：tsvector 全文索引
- **语义搜索**：BGE-M3 向量化 (1024维) + 余弦相似度
- **混合搜索**：RRF 倒数排名融合
- **检索相关性**：稳定 0.8-0.9

## 技能系统

### 核心技能（来自 gbrain）
- `query` — 三层搜索协议（关键字 → 语义 → 结构化）
- `concept-synthesis` — 去重与概念合成（T1 Canon 到 T4 Riff）
- `strategic-reading` — 策略阅读
- `brain-ops` — 大脑运维

### 自定义技能
- `translate-paper` — 论文翻译
- `paper-rewrite` — 论文改写
- `paper-figure-prompt` — 图表提示词生成

## MCP 协议集成

gbrain MCP Server 通过 stdio transport 暴露工具：
- `query` — 混合语义搜索
- `search` — 关键字搜索

Claude Code 和 Hermes Agent 作为 MCP Client 消费这些工具，实现跨系统工具调用互通。

## 数据流

```
1. 写作  →  Obsidian Vault (Markdown)
2. 同步  →  gbrain import/sync
3. 分块  →  Chunking (500字/段)
4. 向量化 → BGE-M3 Embedding (1024维)
5. 存储  →  PGLite + pgvector
6. 检索  →  Hybrid Search + RRF
7. 生成  →  DeepSeek V4 推理
```

## 快速开始

### 环境要求
- Bun ^1.0
- Node.js ^18
- Git
- SiliconFlow API Key (免费)

### 安装

```bash
# 克隆 gbrain
git clone https://github.com/garrytan/gbrain.git
cd gbrain
bun install

# 初始化知识库
bun run src/cli.ts init

# 导入文章
bun run src/cli.ts import /path/to/obsidian/vault

# 启动 MCP 服务
bun run src/cli.ts serve
```

### 配置 Claude Code

`.mcp.json`:
```json
{
  "gbrain": {
    "command": "/path/to/bun",
    "args": ["run", "--cwd", "/path/to/gbrain", "src/cli.ts", "serve"]
  }
}
```

## 致谢

- [Garry Tan](https://github.com/garrytan) — Thin Harness + Fat Skills 架构与 gbrain
- [BAAI](https://github.com/FlagOpen) — BGE-M3 Embedding 模型
- [SiliconFlow](https://siliconflow.cn) — 免费 Embedding API
