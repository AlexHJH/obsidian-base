# MLX 跨机分布式推理落地：jaccl + Thunderbolt RDMA，4 台 Mac Studio 跑 816B
- 来源: https://machinelearning.apple.com/research/mlx-distributed-inference
- 日期: 2026-10-15
- 标签: #MLX #AppleSilicon #分布式推理 #RDMA #M5

## 摘要
Apple 机器学习研究博客给出 MLX 分布式推理的实测口径：当日发布的 MLX（v0.30.1+）新增 jaccl 通信集合，跑在 macOS 26.2 的 Thunderbolt 5 RDMA 驱动上，`mlx.launch` 一条命令就能把 `mlx_lm.benchmark`/`mlx_lm.server` 铺到多台 Mac。4 台 M5 Ultra Mac Studio 以张量并行跑 816B 的 Muse Spark Open（NVFP4）到 36.7 tok/s，单机装不下的模型变成桌面可跑。

## 要点
- 硬件基线：M5 Ultra 统一内存带宽 1.2 TB/s（M3 Ultra 819 GB/s），GPU Neural Accelerator 让 TTFT 最高快 4.6×，decode 快 1.6×；峰值功耗约 300W（M3 Ultra 约 200W）
- Muse Spark Open 816B/A42B：BF16 权重需 >1.6TB，NVFP4 降到约 493GB 仍单机放不下；2 机 pipeline 15.74 → 4 机 tensor 36.73 tok/s @8K ctx，TTFT 8.4→4.8s
- 稠密模型扩展更线性：Qwen3.8-27B 19.69 / 33.27 / 50.89 tok/s（1 机 / 2 机 tensor / 4 机 tensor），Mistral Medium 3.5 128B 7.70 → 23.37
- pipeline 并行对 decode 几乎零收益（层仍串行），但在 65K 上下文 prefill 上快约 1.8×——靠 `--prefill-step-size 8192` 把 chunk 流水起来
- 双雷电线点对点（链路带宽翻倍）有效：4 机 65K prefill，Muse Spark TTFT 50.5s → 40.6s
- MoE 的坑：专家输出归一化带来额外 prefill 通信，2 机 + 65K ctx 时张量并行反而慢于 pipeline；加卡后才反超
- 部署路径：`mlx.launch --hostfile ... --backend jaccl -- mlx_lm.server --port 8080`，再让 OpenCode 之类 agent 客户端指向本地 `127.0.0.1:8080/v1`
- 判据：Mac 侧的分水岭从「单机 decode 吞吐」转到「互联延迟 + 统一内存容量」；RDMA over TB5 是 Apple 版的缩小 NVLink
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
