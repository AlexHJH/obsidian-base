# Agent Plugins 1.0.0：把 Skills 与 MCP server 收进一个可移植包格式

- 来源: https://vercel.com/blog/introducing-agent-plugins ; https://the-decoder.com/amazon-cursor-microsoft-openai-and-vercel-unite-on-a-shared-standard-for-ai-agent-plugins
- 日期: 2026-08-06
- 标签: #AI-Agent #标准 #MCP #Skills #生态

## 摘要
Vercel 发起、AWS / Anysphere(Cursor) / GitHub / Microsoft / OpenAI 共同定稿的 Agent Plugins 1.0.0 发布：一个目录 + `plugin.json` 清单，即可把 Agent Skills 与 MCP server 打包成跨客户端携带的插件。首发即被 ChatGPT/Codex、Cursor、GitHub Copilot、Kiro、VS Code 加载。

## 要点
- 只标准化两件事：组件打包与客户端发现/加载；市场、权限模型、运行时环境明确不在范围内（后续版本再收 hooks 与自定义 agent）
- 治理：TSC 由 AWS / Cursor / Microsoft / OpenAI / Vercel 的 core maintainer 组成，公开开发、声明无单一厂商路线定调
- 与 MCP 的分工是「运行时连接 vs 可移植包」——不是替代；Skills 此前是 Anthropic 走的开放标准，现在进了跨厂商包格式
- 对照：三巨头的扩展层（Claude Code / Cursor / Copilot 各自的插件目录结构）此前互不兼容，这是第一次收敛
- 口径提示：本条为厂商公告 + 媒体转述，未实测各客户端兼容度；「Hermes / OpenClaw 亦支持」出自第三方汇总文，未核实
- 关联 [[AI-Agent-2026格局]]、[[LongLoop Console]]
