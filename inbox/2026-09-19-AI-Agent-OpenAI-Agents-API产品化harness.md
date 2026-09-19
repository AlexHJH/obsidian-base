# OpenAI Agents API：Codex harness 被产品化
- 来源: https://openai.com/index/introducing-the-agents-api/ （细节核校自 cellcog.ai/blog/openai-agents-api）
- 日期: 2026-09-10
- 标签: #AIAgent #Harness #Codex #OpenAI

## 摘要
OpenAI 9/10 公测 Agents API，一句话定位是「用 Codex harness 构建并运行云端 agent，由 OpenAI 全托管」。harness 从「自己写、跟着模型换代重写」变成「一次 API 调用」，环境仍由你选，账单上没有 harness 这一行。

## 要点
- 四个核心对象：agent（模型/instructions/tools/MCP servers）、environment、session、events；入口 `client.beta.agents.sessions.create`，会话可 resume
- 托管 harness 的七件事：执行代码与编辑文件、按目录加载 skills（如 `/workspace/capabilities/skills`）、接 MCP/自定义函数/内置工具、会话中途 steer、**自动 compaction**、subagent 并行（各自独立 context，示例并发上限 3–4）、断点续跑
- 两个省 token 的机制值得抄：**tool search** 按需加载工具定义（降 token 且保留 prompt cache）、**programmatic tool calling**（并行调用、链式组合、在代码里过滤结果）
- 环境三选：OpenAI 托管 sandbox（与 Codex/ChatGPT 同源基建，按容器费率计费）、自托管、9 家 partner（Blaxel/Cloudflare/Daytona/DigitalOcean/E2B/Modal/Oracle/Runloop/Vercel）
- 计价：API 本身不收费，只按所选模型的 token、工具、托管容器时间计费；harness 版本化跟随每次模型发布，底层是开源 Codex harness、OpenAI 运营
- 同期一周盘点：9/3 GPT-6 Astra + Codex CLI 0.153、9/9 CLI 0.154、9/10 Agents API + GPT-Live-1 + ChatGPT for Financial Services
- 对 LongLoop 的启示：compaction / tool search / subagent 独立 context 正是长任务「上下文耐久性」的三个现成手段；验收与 quarantine 这类契约还得自己做，托管 harness 不给
- 关联 [[AI-Agent-2026格局]] [[推理引擎格局-vLLM-SGLang]]
