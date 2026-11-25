# TPU 8i Max 把两颗 die 塞进一个封装，TPU Sync 把 KV 分层到箱外
- 来源: https://blog.google/innovation-and-ai/infrastructure-and-cloud/global-network/tpu-8i-max-and-8tx ；https://newsroom.arm.com/news/google-expands-ai-infrastructure-on-arm-with-tpu-8i-max
- 日期: 2026-11-09（Google 发布）/ 2026-11-10（Arm 口径）
- 标签: #AI芯片 #TPU #KV缓存 #内存墙 #Axion

## 摘要
第八代 TPU 一年内出到第四款：8i Max 是把两颗 8i 拼进同一封装来打内存墙，8tx 是与 8t 同硅片加硬件级机密计算。配套的 TPU Sync 则把 HBM/主机 DRAM/共享存储/数据中心网络做成统一存储层级。

## 要点
- 8i Max 规格（Futurum 引 Google 文档）：双 8i die、384GB HBM、17.2TB/s HBM 带宽、768MB 片上 SRAM = 相对 8i 算力/SRAM/HBM 带宽各翻倍，且沿用原机架占位 → 每机架每瓦算力密度直接乘 2
- 8tx：与 8t 同硅片、搭配第五代 AMD EPYC 主机，数据与权重在使用中保持加密与隔离；复用推理侧的 Boardfly 网络，使微调/蒸馏/RL 不必把敏感数据搬到另一个集群
- 主机 CPU 按 SKU 分叉：8i Max 走 Axion（Arm Neoverse 定制核），8tx 走 AMD EPYC——加速器旁的 CPU 不再是统一选择
- TPU Sync 是最该记的一层：把芯片 HBM、主机 DRAM、共享存储、网络当成一个协同设计的存储层级，毫秒级把大 KV cache 自动分层出片、跨 PD 解耦节点流式传上下文、同步 RL 权重，KV 传输时延与 TTFT 降 90%+
- 判读：Google 对内存墙的解法是"封装内加 die + 软件分层到现成 DRAM/SSD"，而不是等 HBF 这类新介质——这正好压在 HBF 想占的生态位上，HBF 必须在"软件分层已经不够快"的角落证明自己
- 口径：容量/带宽/vCPU 数字均为 Google 与 Arm 公开口径，TTFT -90% 为厂商宣称；无第三方实测

关联 [[AI芯片推理硬件2026全景]]、[[HBF-NAND-AI推理内存2026]]、[[推理引擎格局-vLLM-SGLang]]
