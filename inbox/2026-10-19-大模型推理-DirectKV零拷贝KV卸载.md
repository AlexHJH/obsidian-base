# DirectKV：GH200 上零拷贝 KV 卸载，省 43% 显存、传输量减半（OSDI'26）
- 来源: https://www.usenix.org/conference/osdi26/presentation/luo
- 日期: 2026-07-15（OSDI'26 论文，Seattle）
- 标签: #大模型推理 #KV缓存 #OSDI26 #异构内存 #零拷贝

## 摘要
UVA 的 Shutian Luo / Haiying Shen 指出：现有 KV 卸载（Pie/FlexGen/Neo）都要在 HBM 里留大块 staging buffer，KV 还得先在 CPU→GPU 拷一遍，等于既浪费显存又把传输翻倍。DirectKV 利用 GH200/GB200 的 NVLink-C2C（双向 900 GB/s，PCIe Gen5 的 7 倍）做真零拷贝——attention kernel 直接用指针读 CPU 常驻 KV，靠 SMEM tiling 把压力从互联搬到 HBM。GH200 上传输量最多降 50%、显存降 43%（92GB→47GB），端到端最多快 1.2×。

## 要点
- 关键洞察：naive 零拷贝会崩（PCIe 上慢 20×，NVLink-C2C 上仍慢 2×），因为 GEMM/attention kernel 按 HBM 常驻 operand 调优，反复抓 CPU 内存还拉低 L2 命中；解法是让 SMEM 做流式缓冲，把带宽需求「转移」到 HBM
- 三个机制：CPU-memory-aware tiling、warp 级流水（抓取/计算/回写重叠）、KV projection 与 attention 融合成单 kernel（K/V 留在 SMEM 不回写）
- 副作用收益：零拷贝让动态 KV 块选择只改 kernel 入参、不改 graph 拓扑，CUDA Graph 可原样 replay——swap 方案里 cudaMemcpyAsync 地址每步变，会逼着重捕获
- 实测（GH200 96GB HBM3，Llama-3.1-8B / OPT-13B/30B，ShareGPT+Alpaca，30 req/s）：SGLang 在 13B/30B 高负载直接 OOM，DirectKV 仍 0.75s/token；32K 上下文时对手全 OOM 或大幅劣化
- 边界：显存换容量不是免费午餐——CPU 侧是 pinned host memory（LPDDR5X），只在 C2C 类超片上有意义；PCIe 机器上这套设计前提不成立
- 与 [[HBF-NAND-AI推理内存2026]] 是同一条主线：KV 容量墙的解法在「更快的近存互联」与「更便宜的介质」之间二选一，本文押前者
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]、[[Nvidia-GPU微架构演进]]
