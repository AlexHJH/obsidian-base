# Microsoft Agent Framework 1.0 GA：从 SDK 走到被支持的运行时

- 来源: https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-version-1-0 , https://www.infoq.com/news/2026/08/agent-framework-harness-ga
- 日期: 2026-04-03（1.0 GA，.NET + Python）；Build 2026（6/2–3）后 Agent Harness 与 Foundry Hosted Agents 转 GA
- 标签: #AI-Agent #框架 #运行时 #Agent-Harness

## 摘要
Agent Framework 1.0 于 4/3 GA，把 Semantic Kernel 的企业基座与 AutoGen 的编排思路合并为 .NET/Python 同构 SDK，两个前身转入维护模式。Build 2026 之后重点从「建 agent」转向「跑 agent」：Agent Harness、GitHub Copilot SDK 与 Claude Agent SDK 连接器、Foundry Hosted Agents 相继 GA。

## 要点
- 1.0 承诺稳定 API + 长期支持，多模型连接器覆盖 Foundry/Azure OpenAI/OpenAI/Claude/Bedrock/Gemini/Ollama，跨运行时互操作走 A2A 与 MCP
- Agent Harness 让 agent 拿到 shell、文件系统与消息循环——coding agent / 个人助理范式的本地运行时
- Hosted Agents 按用量计费，本地 agent 几行代码即可转托管；第三方 coding agent 被当作受治理的 fleet 成员
- 选型信号：新项目官方指向 Agent Framework；生产问题从「用哪个框架」变为「在哪执行、能碰什么、如何观测」
- 与本机 LongLoop 同题：harness + 治理 + 可观测，才是运行时该有的三件套
- 关联 [[AI-Agent-2026格局]]
