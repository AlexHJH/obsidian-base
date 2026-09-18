# KV Cache 作为 Agent 运行时：交互性其实是推理引擎问题（Yandex Research）
- 来源: https://research.yandex.com/blog/the-kv-cache-as-an-agent-runtime
- 日期: 2026-09-05
- 标签: #大模型推理 #KVCache #AgentRuntime #交互式推理

## 摘要
Yandex Research 主张：交互能力有一大块是推理运行时问题，不必改权重。KV cache 不是被动的解码优化产物，而是模型的「活动执行状态」——它决定哪些历史计算可见、因果次序如何，因此改变 cache 的结构就能实现训练时不存在的新交互协议。

## 要点
- 核心技巧：利用 RoPE 相对位置性质 ρ(q,i_q)ρ(k,i_k)ᵀ=ρ(q,i_q−i_k)kᵀ，cache block 只按 block-local 坐标存一份，查询时对当前 query 施加 block 专属旋转，同一物理块即可在不同流里呈现不同逻辑偏移（类 paged attention，但同一物理页在每个 worker 的逻辑位置不同）
- Hogwild! Inference：并发运行同模型多实例并写入共享注意力内存，worker 实时看到彼此的部分生成，从而在生成时决定分工/校验/换推导；协作策略由预训练模型提供，运行时只提供并发与可见性
- AsyncReasoning：把推理拆成输入/私有思考/公开输出三流，思考不阻塞表达；实测首个非思考 token 的时间最高降 80×、用户感知总延迟最高降 12×，同时保留大部分推理准确率收益
- 多模态视角：把 agent 视为一组异步 I/O 通道（文本/视觉/音频/思考/语音/动作/工具调用与结果），强制塞进同步请求-响应队列只会造成空转与陈旧状态
- 落点与抽象：正在做 SGLang 实现与多流/多视图自定义 kernel；目标抽象像 OS——流是进程、cache block 是共享内存、attention view 是地址映射、模式切换是调度事件、工具/环境更新是中断
- 已用免训练 Qwen3.5 在 Doom 上验证（画面持续入上下文、思考与动作并发），框架细节待后续论文
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI-Agent-2026格局]]
