# KVServe（SIGCOMM 2026）：把 KV 压缩当运行时决策而非固定配置
- 来源: https://arxiv.org/abs/2605.13734 ; https://huggingface.co/papers/2605.13734
- 日期: 2026-05-13（arXiv v1；已被 SIGCOMM 2026 收录）
- 标签: #KV-Cache #PD分离 #SIGCOMM #压缩

## 摘要
解耦式推理（PD 分离 / KV 状态分离）把 KV 从 GPU 内部状态变成跨网络与存储边界的显式载荷，KV 传输最多占作业完成时间（JCT）的 60%（32K 请求跑 Qwen3-235B、64 节点 prefill 集群时 KV 出口带宽需求达 2.1Tbps）。中科院大学 + 计算所 + 上交的 KVServe 主张压缩不是写死的配置，而应随服务状态动态选择策略，被 SIGCOMM 2026 收录。

## 要点
- 三段式设计：统一压缩策略空间（含新组件与跨方法重组）→ 离线 profiling 生成 profile 且搜索开销低 → 在线 Service-Aware Controller（解析延迟模型 + 轻量 bandit）在约束下选 profile，并纠正 offline-online 失配
- 结果（已集成 vLLM；3 模型 / 6 数据集 / 4 测试床 / 3 基线）：PD 分离下 JCT 最高 9.13× 加速（≤5Gbps 受限带宽下最高 9.2×）；KV 解耦 + 远程前缀缓存下 TTFT 最高降 32.8×，且始终守住严格 SLO
- 自适应规避「负优化」：带宽充裕时自动识别「省下的通信抵不上算力成本」而绕过压缩——这正是静态方法的典型翻车点
- 精度：MixHQ 量化器 + 贝叶斯搜索，最高 10× 压缩下相对精度保持 99% 以上
- 同 session 的 DualPath（北大 + DeepSeek-AI）走另一条路：直接回收解耦 KV 存储的 I/O 来加速 agentic 推理
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
