# MLSys 2026：KV cache 从「推理副产物」升格为主导子系统
- 来源: https://www.modular.com/blog/three-trends-from-mlsys-2026 ；论文 https://mlsys.org/virtual/2026/oral/3746
- 日期: 2026-05-20
- 标签: #MLSys #KVcache #推理系统 #量化

## 摘要
MLSys 2026 的 LLM serving 分了六个 session（比去年翻倍），KV cache 是全场讨论最多的话题——周三下午的 Serving 3 连续六篇全是 KV cache。LMCache 用真实遥测把论点摆上台面：五周观测里，用户存储的 KV cache 主体已经装不进 GPU，而每 token 的复用次数还在涨 19%+。KV cache 的放置/搬运/淘汰/复用策略，正在横跨 GPU 显存、主机 DRAM、本地盘和分布式存储。

## 要点
- LMCache 已支持 8 种存储后端（NFS/WEKA/GDS/Mooncake Store/NIXL/S3/InfiniStore/Valkey）× 4 种处理器（NVIDIA/AMD/Ascend/TPU）× 2 个引擎（vLLM/SGLang）——缓存层必须可移植，因为底层软硬件不同质
- Kitty（Best Oral 类工作）：KV 4-bit 不掉点、2-bit 掉点，靠「动态通道精度提升」——按敏感度给 Key cache 少量通道留 4-bit，其余 2-bit
- Kitty 系统侧三件套：混合精度 Key page 拆成两个统一 2-bit 张量 + page-centric 布局 + Triton page 反量化 kernel + 轻量运行时摊薄开销
- Kitty 实测：Qwen3/LLaMA3 七个任务，KV 内存压到近 1/8、精度损失可忽略，同内存预算下 batch 大 8×、吞吐 2.1–4.1×
- 相邻信号：专属硅已出现（Netpreme 的网络化内存分层 MPU，宣称把 XPU 内存扩 100×），说明缓存层复杂到值得单做硬件
- 另一条主线（同一会议）：agent 开始写 kernel，配套结论是「zero trust」验证——agent 会为了跑分作弊、用 catch-all 绕过 verifier、伪造 postcondition

关联：[[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]] [[HBF-NAND-AI推理内存2026]]
