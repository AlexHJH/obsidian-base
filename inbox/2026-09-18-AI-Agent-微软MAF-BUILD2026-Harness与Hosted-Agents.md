# 微软 Agent Framework @ BUILD 2026：Harness 成为一等公民
- 来源: https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-at-build-2026-announce
- 日期: 2026-06-03
- 标签: #AIAgent #Harness #Microsoft #多Agent

## 摘要
MAF（.NET/Python 同构 SDK+运行时，2026-04-02 GA，AutoGen 与 Semantic Kernel 的收敛体）把原本散落在各处的「harness 工程」模式收进框架：一个方法即可把任意 chat client 升级为完整 harness，并提供 Foundry Hosted Agents 作为生产归宿。

## 要点
- 一行方法生成 harness：自动上下文压缩（循环中途按 token 用量压缩历史）、内置默认指令并做指令合并（harness 指令在前、自定义在后）
- 内置 Provider/工具：FileMemoryProvider（`agent-file-memory/{session}/` 会话级文件记忆）、FileAccessProvider、TodoProvider、AgentModeProvider（plan/execute 分模式）、AgentSkillsProvider（文件系统技能发现）、BackgroundAgentsProvider（子 agent 并行扇出）、托管 web search、.NET 侧沙箱 ShellExecutor
- 中间件层：ToolApprovalAgent（敏感调用的「不再询问」审批规则）、OpenTelemetryAgent（按 Semantic Conventions 自动追踪）、存储后端可插拔（FileMemoryStore/FileAccessStore → 任意 AgentFileStore，如 blob）
- Foundry Hosted Agents：自己的代码打成容器部署到托管基础设施，自带身份、自动扩缩、托管会话状态、可观测与版本化，空闲可 scale-to-zero
- 信号：harness 内部六件套（记忆/上下文压缩/todo/模式/技能/子 agent）已成厂商标配，框架竞争点从「抽象好不好用」转向「工程模式是否开箱可用」
- 关联 [[AI-Agent-2026格局]]
