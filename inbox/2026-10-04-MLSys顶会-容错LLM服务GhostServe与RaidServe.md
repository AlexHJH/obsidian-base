# MLSys 2026 新增「容错」方向：GhostServe 与 RaidServe

- 来源: https://paper.lingyunyang.com/reading-notes/conference/mlsys-2026 , https://arxiv.org/abs/2605.00831
- 日期: 2026-10-04（MLSys 2026，5/18–22）
- 标签: #MLSys2026 #LLM服务 #容错 #KVCache #高可用

## 摘要
MLSys 2026 单列 Fault Tolerance 方向，两篇代表工作：GhostServe（UCF）用纠删码在 host memory 的「影子」里保护流式 KV cache，设备故障时快速重建；RaidServe（Stanford）面向张量并行的容错服务，GPU 故障时靠计算与显存再平衡维持性能。

## 要点
- GhostServe 机制：不做全量重算、也不做状态复制，而是对 streaming KV cache 施加 erasure coding，把 parity shard 存到 host memory，故障后重建丢失的分片
- GhostServe 实测（含故障场景）：checkpoint 延迟最多降 2.7×、单 batch 恢复延迟降 2.1×、中位响应延迟 1.2×
- 目标场景：保护长跑 agent 任务，抵抗硬件/软件故障——与「agentic 长任务」趋势直接呼应
- RaidServe：tensor-parallel 服务于 GPU 故障下通过 computation + memory rebalancing 保持高性能
- 趋势判断：LLM 服务评价维度从纯「性能/吞吐」扩展到「韧性/高可用」，生产化信号
- 关联 [[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]]
