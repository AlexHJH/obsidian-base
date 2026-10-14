# WWDC26：MLX 上分布式推理——RDMA over Thunderbolt 5 与 JACCL 集合通信
- 来源: https://developer.apple.com/videos/play/wwdc2026/233 ; https://developer.apple.com/videos/play/wwdc2026/232
- 日期: 2026-06（WWDC26 session 233 / 232）
- 标签: #MLX #AppleSilicon #分布式推理 #RDMA

## 摘要
Apple 在 WWDC26 把「多台 Mac 当本地集群」补成完整栈：macOS 26.2 起支持 RDMA over Thunderbolt 5，Apple 自研 JACCL 提供集合通信原语，MLX 基于 JACCL 做分布式推理/训练编排，mlx.launch + hostfile 即可跨机拉起。演示中一个万亿参数模型跨多台 Mac 本地跑推理，LoRA 微调从单台 M3 Ultra 约 180 tok/s 提升到集群约 600 tok/s（3×+），代码改动极小。

## 要点
- 分层：互连（Thunderbolt）+ 传输（RDMA over TB5）+ 通信库（JACCL）+ 框架/编排（MLX、mlx.launch、MLX LM 内置 server）
- `--auto-setup` 会 SSH 探测各机、发现 Thunderbolt 拓扑、关闭 TB Bridge 并逐链路配 RDMA，最后生成 JSON hostfile；不加该 flag 则只打印命令供人工审阅
- JACCL 不限 ML，任何 Apple Silicon 分布式负载都可用；配套 session 232 讲四层栈（MLX → MLX-LM → OpenAI 兼容 MLX-LM Server，含结构化 tool calling → Ollama/LM Studio/vLLM 等 agent 层）
- 意义：本地集群把「私有 + 长上下文/大模型」从单机内存上限里解放出来，瓶颈转向 TB5 链路带宽与跨机 KV/权重切分策略
- 口径：演示为 Apple 自家 session，未见第三方复现；跨机方案依赖 macOS 26.2+ 与 TB5 硬件
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]、[[AI-Agent-2026格局]]
