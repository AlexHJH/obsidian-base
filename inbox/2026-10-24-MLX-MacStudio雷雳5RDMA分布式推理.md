# Apple 官方：两台 Mac Studio 用 Thunderbolt 5 RDMA 做分布式推理，MLX 补齐 JACCL 集合通信
- 来源: https://machinelearning.apple.com/research/mlx-distributed-inference
- 日期: 2026-10-15
- 标签: #MLX #AppleSilicon #分布式推理 #RDMA

## 摘要
macOS 26.2 引入 Thunderbolt 5 上的低延迟 RDMA 驱动，MLX v0.30.1+ 在其上提供集合通信库 `jaccl`（读作 jackal），配合 `mlx.launch` 一条命令即可切到张量并行。Apple 实测环境是两台 Mac Studio 各用两根 TB5 线互联（叠带宽），走 peer-to-peer mesh。

## 要点
- 通信层是自底向上的三段：RDMA over Thunderbolt → JACCL 集合原语 → MLX / MLX LM，JACCL 本身不绑定 ML，任何分布式负载都能用
- JACCL 按消息大小和算子类型自动选拓扑：延迟敏感走 mesh，带宽敏感走 ring
- 机器描述用一份 JSON 数组，每节点含 `ssh` / `ips` / `rdma` 三类字段，可用 `mlx.distributed_config` 生成
- 社区实测 RDMA 文件传输 3.5+ GB/s；TB5 名义 80Gbps，实际 TCP 约 60Gbps（7.5GB/s）
- 瓶颈判据：TP 下每 token 每层约需 all-reduce 16MB 激活，带宽而非算力是天花板
- 坑：分布式 RDMA 推理早期存在 GPU-CPU 内存一致性导致的挂死/输出损坏，需 `__dsb(0xF)` 屏障修复
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
