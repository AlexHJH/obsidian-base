# Apple：MLX 在 Mac Studio 上做单机与分布式大模型推理（RDMA over Thunderbolt）

- 来源: https://machinelearning.apple.com/research/mlx-distributed-inference
- 日期: 2026-10-15
- 标签: #MLX #AppleSilicon #分布式推理 #RDMA #MoE

## 摘要
Apple ML Research 用 mlx_lm 在 M5 Ultra Mac Studio 上跑三档模型（Muse Spark Open 816B MoE、Mistral Medium 3.5 128B、Qwen 3.8 27B）。128B 以下单机 512GB 统一内存即可放下；816B 即使 NVFP4 量化到约 493GB 也必须跨机，靠 macOS 26.2 的 Thunderbolt 5 RDMA 驱动 + MLX jaccl 做 pipeline / tensor 并行，四机达 36.7 tok/s。

## 要点
- 两条性能定律被分开点名：TTFT 是 compute bound，M5 的 GPU Neural Accelerator 带来最高 4.6×；decode 是带宽 bound，1.2TB/s（M3 Ultra 819GB/s）→ 1.6×
- jaccl 三种并行：pipeline 只交换层间中间输出，decode 几乎不掉速，但 65k 长上下文 prefill 因分块流水重叠最高 1.8×；tensor 并行每 token 每层都要 broadcast/all-reduce，强依赖低延迟链路
- 反直觉点：Muse Spark Open 在两机 tensor 并行下 65k prefill 反而慢于 pipeline（专家输出需单独 normalize，多一次通信），加到四机才翻盘
- 双线缆 mesh 有效：四机 65k prefill TTFT 从 50.5s（单线）降到 40.6s（双线）
- 分布式 server 只需在 mlx_lm.server 前加 `mlx.launch --backend jaccl`，即可给 OpenCode 一类本地 agent 供模型；`MLX_METAL_FAST_SYNCH=1` 降 CPU-GPU 同步开销
- 功耗：M5 Ultra 峰值约 300W、M3 Ultra 约 200W —— 桌面级部署的实际边界
- 口径：全部为厂商自测（mlx_lm.benchmark，8k/65k 上下文、batch=1、生成 128 token），无第三方复现；Muse Spark Open 走 NVFP4，与 FP8/BF16 原生精度模型不可横向比
