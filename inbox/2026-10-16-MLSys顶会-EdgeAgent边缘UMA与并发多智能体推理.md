# EdgeAgent：把边缘 UMA 上的并发多智能体推理做成跨层系统

- 来源: https://arxiv.org/abs/2610.03394
- 日期: 2026-10-05（arXiv 2610.03394，cs.DC）
- 标签: #系统顶会 #大模型推理 #UMA #零拷贝 #投机解码

## 摘要
EdgeAgent（Yuhai Long、Yuanxin Wei、Kai Wu、Jinhui Wei、Dan Huang、Jiangsu Du）针对「端侧隐私 + 多智能体协作」：UMA 设备上 decode 访存受限、CPU-GPU 朴素并行会让总线互相争抢，叠加工具调用造成的频繁停顿与投机解码草稿难度剧烈波动（推理型 vs 结构化生成），静态批处理基本失效。

## 要点
- 微架构层：绕开图编译器的刚性约束，实现零拷贝 UMA-aware 张量并行，用**非对称内存布局**同时喂饱 CPU 与 GPU 计算单元
- 调度层：按实时序列可预测性动态分配 draft 预算，为带宽浪费封顶；配 stall-aware 的 suspend-and-yield 与异步机制应对工具停顿
- 问题定义：多智能体 ≠ 单请求批量——agent 间协作让执行高度碎片化，首要约束是内存带宽而非算力
- 读法：与 [[推理引擎格局-vLLM-SGLang]] 的 DCP/分离式路线互补——数据中心靠拆集群，端侧要把 CPU/GPU 统一编址「合用一条总线」；对 GP+CC 分离 SoC 的 UVA 设计有直接参照
- 口径：摘要与 intro 取自 arXiv HTML 的搜索摘要，性能数字未复现；作者单位未在检索结果中出现
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[MLX生态2026]]
