# CacheScout：让调度器在线学习 Agent 执行路径来管 KV Cache
- 来源: https://www.alphaxiv.org/abs/2608.14624
- 日期: 2026-07-16
- 标签: #大模型推理 #AgenticServing #KVCache #调度

## 摘要
论文（UCSC / UW / 芝大，Junchen Jiang、Liting Hu 等）关注 Agentic 服务里的缓存低效：多 Agent 框架（如 AutoGen）在运行时动态派发任务，下一步执行哪个 Agent 事先未知，导致已算好的 KV Cache 无法复用、显存被无用前缀长期占用。CacheScout 在线学习 Agent 之间的转移概率，据此决定缓存保留/淘汰与预取。

## 要点
- 与 KVFlow 的关键差异：KVFlow 要求静态定义的执行图，CacheScout 面向 LLM 自主决策、路径不可预知的动态工作流
- 思路：把「Agent 调度决策」当作可学习的执行轨迹信号，而不是靠固定启发式
- 方向印证：KV Cache 管理正从「按前缀命中率」转向「按未来执行概率」——服务系统开始预测工作流，而不只是缓存历史
- 关联 [[大模型推理-SGLang-DeepSeekV4百万上下文优化]] [[AI-Agent-2026格局]] [[推理引擎格局-vLLM-SGLang]]
