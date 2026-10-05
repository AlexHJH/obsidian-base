# Claude Agent SDK：子代理隔离边界与 Agent 额度单列
- 来源: https://tech-insider.org/au/claude-agent-sdk-subagents-multi-agent-2026 ; https://www.developersdigest.tech/blog/claude-agent-sdk-vs-claude-code
- 日期: 2026-09（计费变更 2026-06-15 生效）
- 标签: #AI-Agent #Claude #harness #子代理

## 摘要
Claude Agent SDK（2025-09 由 Claude Code SDK 更名）把 Claude Code 的 harness 抽成库：主 agent 可派生子代理，每个子代理跑在独立会话、独立 system prompt、独立工具与权限集里，只有最终消息回传父会话——父上下文不被子代理的中间过程污染。

## 要点
- 隔离是核心语义：子代理读不到也污染不了父上下文，工具清单收窄以限制爆炸半径；消息带 `parent_tool_use_id` 做归属与成本追踪；会话靠 init 系统消息里的 `session_id` 续跑
- 与 Claude Code 的边界：需要「驱动」用 Code，需要「构建产品」用 SDK；SDK 原生消费 MCP server，与 A2A 互通
- 计费/合规：2026-06-15 起 Agent SDK 与 `claude -p` 单列额度，不再吃订阅交互额度；对外发布的产品必须用 API key 认证，不得给终端用户放 claude.ai 登录
- 约束：SDK 只适配 Claude 模型，要模型可换得走 LangGraph 一类编排层
- 对本机启示：subagent 隔离 + SubagentStop 式回传契约，正是 LongLoop「验收契约 / 防虚假完成」要的边界
- 口径：依据第三方汇总与官方文档转述整理，未逐字核官方原文
