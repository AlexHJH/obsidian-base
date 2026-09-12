# OpenAI 公测 Agents API：把 Codex 的 harness 变成产品
- 来源: https://openai.com/index/introducing-the-agents-api
- 日期: 2026-09-10
- 标签: #AI-Agent #OpenAI #AgentHarness #MCP

## 摘要
OpenAI 9/10 上线 Agents API 公测，把驱动 Codex 的托管 harness 直接暴露为 API：开发者只给任务、模型、工具与执行环境，编排、长会话、上下文压缩与失败恢复由 OpenAI 侧负责。

## 要点
- harness 即产品：sessions、orchestration、context compaction、tool search、subagents、recovery 全部托管
- 内置沙箱执行代码、文件编辑、MCP 连接、产物生成、多 Agent 委派；支持自托管沙箱（workspace / capability 目录）
- 计费无 API 附加费，按 token + 工具用量标准价结算
- 信号：Agent 竞争从「选框架」转向「harness 工程质量」，与微软 Agent Framework Harness、Cloudflare Project Think 同构
- 关联 [[AI-Agent-2026格局]]、[[大模型推理-Agentic 服务优化]]
