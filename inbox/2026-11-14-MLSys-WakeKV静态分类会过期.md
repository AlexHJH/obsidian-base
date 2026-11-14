# WakeKV：一次分类定终身的 head 放置会过期

- 来源: https://arxiv.org/abs/2610.02713 (NeurIPS 2026 ML for Systems Workshop)
- 日期: 2026-10-02
- 标签: #MLSys #KV-cache #LLM推理 #NeurIPS

## 摘要
针对 FlexiCache 这类"离线或 prefill 期定一次 head 类型、生成全程不变"的做法，作者实测三个模型（1.5B–8B）× 三种负载（needle 检索、长 CoT、多轮回忆），发现多数 head 在生成过程中至少改变一次读取行为。于是把策略改成反应式：冷却的 head 不冻结也不永久驱逐，而是移到可恢复的 CPU 水库。

## 要点
- 评测覆盖 5 个 model-regime 组合，基线 SnapKV、uniform R-KV、ReasonAlloc：等内存/等预算下 miss rate 一致优于冻结分类与破坏性驱逐。
- 真机验证直接跑在 FlexiCache/vLLM 实现上（Mistral-7B），吞吐提升且 LongBench 质量保持——即"在你的实现上改策略"，不是另起炉灶。
- 结论对系统设计的含义：head 重要性是时变量，任何静态分类都是在赌负载；分层内存管理的正确形态是"可逆驻留 + 反应式降级"。
- 与 [[FlexiCache头部时间稳定性]] 恰成一对：前者给 70% 显存收益的强度，后者给它的寿命上界。MLSys 2026 把 KV cache 当主子系统的判断（见 [[MLSys2026论文精选]]）在这里得到实证。
