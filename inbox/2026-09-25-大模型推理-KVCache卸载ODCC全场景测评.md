# KV Cache 卸载进入生产：ODCC 全场景测评的量化结论

- 来源: https://www.xsky.com/about/news/info/id/1046.html
- 日期: 2026-07-02
- 标签: #大模型推理 #KVCache #存储卸载 #DPU #ODCC

## 摘要
ODCC AI 存储实验室联合 NVIDIA、XSKY（存储引擎 MeshFusion）、大普微，完成面向多部署形态、多模型架构、多存储策略的 KV Cache 评测：把 KV Cache 卸载到 SSD 池，长序列场景 TTFT 从数十秒降到百毫秒级，TPS 提升一个数量级（约 10–32×，Cache 命中时 7 万–11 万 tokens/s）。架构上基于 BlueField-3 DPU + JBOF，数据路径绕开 host CPU/内存直达显存，形态对齐 NVIDIA CMX。

## 要点
- 代价结构清晰：EC 4+2:1 纠删码与单副本性能基本持平 → 生产可直接用 EC 换来容量利用率与可靠性，打破「EC 必然拖慢」的旧认知
- 网络不是瓶颈：Spectrum-X 下集群 EC 顺序读 272.1 GiB/s，达三存储节点物理极限的 97.4%，可随网口数线性扩容
- 工程门槛在语义而非带宽：KV 张量带位置信息、移动即失效，缓存层要介入调度决策，故障还可能表现为静默错误
- 后续演进：已支持混合注意力模型（KV 变异构缓存）、完成昇腾 910C 与阿里真武 810E PPU 适配（8 月），跨 GPU/NPU 双生态
- 关联 [[推理引擎格局-vLLM-SGLang]] [[#华为发布 OceanStor M900 Context Memory Storage：PB 级 KV Cache 共享内存层]]
