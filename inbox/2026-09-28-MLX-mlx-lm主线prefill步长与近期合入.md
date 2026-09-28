# mlx-lm 主线：`--prefill-step-size` 成为显式旋钮 + 近期模型/工具链合入批次

- 来源: https://github.com/ml-explore/mlx-lm , https://github.com/ml-explore/mlx-lm/releases
- 日期: 2026-09-28（README / releases 页面现状，无新 tag，能力仍看 main）
- 标签: #MLX #Apple-Silicon #预填充 #长上下文 #推理服务

## 摘要
mlx-lm 把「长 prompt 分块读取」从内部实现提升为命令行参数 `--prefill-step-size n`（默认 2048），`generate / chat / server / benchmark` 全部接受：步长越小峰值内存越低、预填充越慢，是 Mac 上「长上下文 vs 显存」的直接权衡开关。同期主线合入一批模型与工具解析支持（Kimi Linear、LongCat MLA、DeepSeek V3.2 提速、Qwen3.5 无视觉版、LFM2/Mistral 工具解析）。

## 要点
- prefill 分块语义：长 prompt 按 step 逐段读入，step 决定单次处理的 prompt token 数——内存敏感场景（如 M 系列统一内存吃紧、并发多会话）应把它调小
- 模型面新增/修复：Kimi Linear（#853）、LongCat MLA（#868）、qwen3.5 无视觉系列（#869）、**Faster DSV32 generation（#885）**、DeepSeek V3.2 indexer 与权重加载修复（#866）、step3p5 滑窗层改用 rotating cache（#949）
- 工具调用：LFM2 支持「pythonic」工具调用（#864）、Mistral 工具解析（#874）——本地 Agent 的工具链兼容面继续扩大
- 质量/默认值坑：`convert()` 量化模式默认值修正（#935）、`evaluate` 无用 batch_size 参数修复（#762）、qwen3.5 sanitize（#928）
- 服务端缓存治理：批处理生成时清空 cache（#926）、eval 前写入 token 避免超大计算图（#924）——直指「批处理 + KV 复用」组合下的内存与图规模问题
- 口径提醒：releases 页最新 tag 仍落后于 main（呼应 [[MLX生态2026#mlx-lm 九月迭代：Mistral4 / Spark-X2.5 支持与社区多模态量化（2026-09-25 归档）]]）——判断能力要读 commit 而非 tag
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
