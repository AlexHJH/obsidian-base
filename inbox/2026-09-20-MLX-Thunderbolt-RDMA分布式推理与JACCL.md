# MLX 分布式推理：Thunderbolt 5 上的 RDMA + JACCL，四节点提速约 3 倍
- 来源: https://developer.apple.com/videos/play/wwdc2026/233
- 日期: 2026-06-08
- 标签: #MLX #AppleSilicon #分布式推理 #RDMA #JACCL

## 摘要
WWDC26 Session 233 把 MLX 的分布式能力正式产品化：从 macOS 26.2 起，Apple Silicon 支持 Thunderbolt 5 上的 RDMA，数据直接从一台机器的内存搬到另一台，绕开大部分 CPU 与 OS 开销。在此之上 Apple 开源了集合通信库 JACCL，并提供 `mlx.launch` + JSON hostfile 的集群编排。官方实测四节点分布式推理最高约 3 倍加速，可运行单机装不下的万亿参数级模型。

## 要点
- 三层栈：物理互连（Thunderbolt 5 线缆，可组 mesh 或 ring）→ 传输协议（RDMA over Thunderbolt，macOS 26.2+，需手动启用并重启）→ 通信后端（JACCL，提供 group 内集合通信原语，非 ML 专用）。
- 免手工配置：`mlx.launch --auto-setup` 会自动 SSH 探测各机、关闭 Thunderbolt Bridge、为每条链路配置 RDMA，并生成 hostfile（含 ssh 主机名、局域网 IP、RDMA 设备名）。
- 拓扑可调：延迟敏感选 mesh，带宽敏感选 ring。示例用四台 M3 Ultra 组网。
- 对 agent 的两点价值：一是跑单机放不下的更大模型（官方举 DeepSeek 1.6T，权重就需 800GB+）；二是把 prefill 并行化，直接加速 agentic loop 里的工具结果处理。
- 配套：M5 的 Neural Accelerators 用于 prompt processing，官方称矩阵乘法较 M4 快约 4 倍。

## 关联
[[MLX生态2026]] [[推理引擎格局-vLLM-SGLang]]
