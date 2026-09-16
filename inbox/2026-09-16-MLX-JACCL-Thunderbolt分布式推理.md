# MLX 分布式推理：JACCL + Thunderbolt RDMA 组多机 Mac 集群
- 来源: https://developer.apple.com/videos/play/wwdc2026/233
- 日期: 2026-09-16
- 标签: #MLX #AppleSilicon #分布式推理 #RDMA #JACCL

## 摘要
WWDC26 session 233 讲清了 Apple Silicon 的分布式栈：RDMA over Thunderbolt → JACCL 集合通信库 → MLX → mlx-lm，用 `mlx.launch` 加一份 hostfile 就能把模型自动分片到多台 Mac。MLX v0.30.1+ / macOS 26.2+ 起支持，社区实测四节点最高约 3x 加速，TB5 直传文件带宽已达 3.5+ GB/s。

## 要点
- 拓扑可切：延迟敏感用 mesh、带宽敏感用 ring，JACCL 两种都支持
- 落地坑（mlx issue #3207）：`rdma_enN` 设备名与 TB5 网口 IP 每次重建 mesh/重启都会变，hostfile 里的 10GbE IP 才是稳定标识，且 hostfile 必须存在于每个节点而非只在 launcher
- PD 泄漏：每次 `mx.distributed.init()` 申请 RDMA protection domain 却释放不完全，逐文件建 session 很快撞内核上限，只能靠重启恢复 → 一次 session 把所有文件传完
- 稳定性：社区有 4×M3 Ultra 在 macOS 26.4.1 上 RDMA 报 errno 60、切 ring 正常的报告，生产用需锁版本
- 关联: [[MLX生态2026]]
