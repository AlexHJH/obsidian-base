# GPT-Live-1 进 API：全双工语音按秒计费，推理外包给后端模型
- 来源: https://cellcog.ai/blog/gpt-live-1 （原始公告 openai.com，2026-09-10）
- 日期: 2026-09-10
- 标签: #大模型推理 #语音 #RealtimeAPI #部署

## 摘要
OpenAI 9/10 把 ChatGPT 里的全双工语音模型放进 API：model id `gpt-live-1`、端点 `v1/live/sessions`，边听边说、自带打断与背景噪声处理，定价 $0.05/分钟且按秒计费。架构上它只做对话，推理与工具调用委派给你指定的 backend 模型。

## 要点
- 双层账单是这个产品的核心结构：语音层 $0.05/分钟（按秒计，不向上取整）+ 后端推理模型按自身 token 费率 + 工具/电话线/存储/应用基建，总成本是两项相加而非单一数字
- 前端后端解耦：voice layer 负责听说与打断，把 reasoning/tool use 交给另一个模型，所以「想」的时候对话可以继续——这是对 Realtime API（单模型兼做语音+推理、按音频 token 计费）的正面替代
- 接入面：WebRTC、WebSocket、sideband 服务端控制、telephony/SIP；输入输出支持文本+音频，不支持图像/视频；无免费额度，按并发会话限流，knowledge cutoff 2025-07-31
- 与 per-token 计费对比：Realtime（gpt-realtime-2.1）旗舰约 $0.06–0.11/分钟（未缓存长通话可达 $0.18–0.46），按分钟计费把「说多久」的成本变得可预测
- 工程含义：语音 Agent 的成本瓶颈从音频 token 变成「会话时长 × 后端推理量」，并发会话数成为新的配额单位
- 对本地语音服务（Qwen3-ASR+TTS/MLX）的对照：云侧用 per-second 计时把交互延迟卖成钱，本地路线的价值锚点就是零边际成本 + 隐私；两层架构（轻前端 + 大后端）本地同样可复刻
- 关联 [[推理引擎格局-vLLM-SGLang]] [[AI-Agent-2026格局]] [[MLX生态2026]]
