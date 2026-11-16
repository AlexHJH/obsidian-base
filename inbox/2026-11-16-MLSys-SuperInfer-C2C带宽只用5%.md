# SuperInfer：GH200 的 NVLink-C2C 被现有栈用掉不到 5%

- 来源: https://arxiv.org/abs/2601.20309 （MLSys 2026）
- 日期: 2026-05-18 (v2)
- 标签: #MLSys #LLM推理 #KV-Cache #SLO #异构内存

## 摘要
面向 GH200 这类 GPU-CPU 同板「超级芯片」的 LLM 服务系统。作者指出上限 900 GB/s 的 NVLink-C2C 在现有 offloading 栈里利用率不足 5%（被当 PCIe 使），于是把抢占式换出改造成主动轮转：RotaSched 按 SLO 进度决定谁留在 GPU，DuplexKV 做全双工 C2C 传输。

## 要点
- 结果：高请求率下 TTFT SLO 达成率最高 +74.7%，TBT 与吞吐与 SOTA 持平；低负载时与 baseline 打平——收益全部来自内存管理而非算力。
- 关键洞察：瓶颈在软件栈不在互联，与 [[NVIDIA-Tensor-Core-TMA与AI指令体系]] 里「TMA 之前靠 cp.async 手工凑流水」是同一类错误。
- 相对 Pie 的差异：Pie 也会把 KV 溢写到 CPU，但没有 SLO 感知调度，也没有吃满 C2C。
- 追问点：这套设计对非 GH200（如国产 GP+CC 分离 SoC）能迁移多少，取决于 C2C 是否 cache-coherent。
