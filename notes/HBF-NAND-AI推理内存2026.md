# HBF (High Bandwidth Flash) — AI 推理内存新层级

> 种子文档（2026-08-11 创建）。后续由知识库采集 cron 每天更新进展。

## 一句话

把 NAND 用 HBM 的封装工艺（TSV 堆叠）做成新的内存层级：**容量 8–16x HBM、带宽接近 HBM、延迟 ns 级（HBM）→ μs 级（NAND）、写耐久低、功耗偏高**。定位是"推理专用内存层"，不是 HBM 替代品。

## 关键参数对比

| | HBM4 | HBF | Kioxia 原型 |
|---|---|---|---|
| 容量 | 48–64GB | ~8–16x HBM | 5TB |
| 带宽 | 1.6–2 TB/s | 接近 HBM | 64 GB/s |
| 延迟 | ns | μs | μs |
| 功耗 | — | ~4x HBM/bit | <40W |
| 写耐久 | ∞ | 低（NAND 本质） | — |

## 玩家格局（截至 FMS 2026）

- **SanDisk + SK Hynix 联盟**：2025 MoU → 2026.2 OCP 标准化项目（Google、Tenstorrent 已加入）。样品 **2026 H2**，AI 推理设备样品 **2027 初**，~2028–2030 放量。Micron 缺席联盟。
- **Samsung 单干**：FMS 2026 主推 zHBM（HBM 直接堆 GPU 上，宣称 ~8x HBM5 性能、10x 密度）+ V10 BV-NAND（400+ 层）。
- **Kioxia**：2025.8 原型——5TB / 64GB/s / <40W，PCIe 6.0 + 128Gbps PAM4 菊花链控制器，模块化路线。

## LLM 推理应用

1. **模型权重**：冻结、只读、确定性访问 → 完美匹配 NAND。每节点权重容量 ×10，直接减少所需 GPU 数量。
2. **冷 KV cache**：Llama 4 的 10M token 上下文需 5.4TB KV cache。HBF 承接冷 KV，HBM 只做热工作区。
3. **H³ 论文（SK Hynix+KAIST, IEEE）**：HBM+HBF 混合推理，性能/瓦 +2.69x；10M token KV 场景并发查询 18.8x；32 GPU 负载 → 2 GPU。
4. **软肋**：KV cache 每 token 新写，NAND 耐久扛不住 → 需写合并/追加写策略（H³ 已处理）。

## 对系统架构的影响（异构 SoC 视角）

- 内存层级 HBM/DRAM/SSD 三级 → **HBM + HBF + SSD 四级**，HBF 是贴加速器的高带宽持久层
- 权重确定性访问 → 硬件预取/调度可提前规划，SoC 调度器新战场
- 需新内存控制器接口（NAND 协议 + 高带宽 TSV）+ 写合并、磨损均衡软件栈
- CXL 内存池同生态位竞争，但带宽差一个量级

## OCP 首版技术规范发布（2026-08-11 归档）
整理自 [[inbox/2026-08-11-HBF-OCP规范发布]]
- 2026-08-03 SanDisk × SK hynix 通过 OCP 发布 HBF™ 首版技术规范（距 2 月启动标准化项目仅 6 个月）
- 单单元最高 512GB / 1.638 TB/s，仿真显示权重读取性能距"无限容量 HBM"仅差 2.2%
- FMS 2026 同场：SK hynix 首秀第 10 代 375 层 4D NAND（每瓦性能 2.5×）；Samsung 主推 zHBM，自 2020 年代初已布局 HBF 专利

## Google DeepMind 站台 HBF（2026-08-13 归档）
整理自 [[inbox/2026-08-13-HBF-谷歌DeepMind站台]]
- FMS 2026（8/6）圆桌「用 HBF 打破内存墙」：Google DeepMind 资深工程师马晓宇为 HBF 背书——LLM 推理数据量激增，需高容量+高带宽+高能效的新内存层级；Google 与 Tenstorrent 已加入 HBF 联盟
- 读中心模型：HBF 主「读」、HBM 管热工作区，联用规避 NAND 写耐久短板（SanDisk VP 现场回应）
- 马晓宇 & Patterson 的 IEEE Computer 论文：HBF 用于冻结权重（~512GB vs HBM4 48GB），推理瓶颈在内存/互联而非算力

## FMS 2026 规格补充：8/16 层堆叠 + 三档带宽 + 分层内存（2026-08-19 归档）
整理自 [[inbox/2026-08-16-HBF-FMS技术规格分层内存]]
- 补充 [[#OCP 首版技术规范发布（2026-08-11 归档）]] 配置细节：8 层与 16 层 NAND 堆叠两种规格，最大 512GB，带宽分三档（0.4–3.0 TB/s），以 UCIe 连接 CPU/GPU
- SK 海力士副社长金千成提出「分层内存(Tiered Memory)」：代理式 AI 时代需整合互补存储器协同优化；定位 HBM 与 SSD 之间新层级，覆盖快速数据湖/模型存储/RAG/KV cache
- 规范为 OCP 开放标准（非私有），联盟已有 Google、Tenstorrent，加速生态规模化落地

## Kioxia 送样第 9 代 BiCS 1Tb TLC：HBF 容量底座升级（2026-08-21 归档）
整理自 [[inbox/2026-08-18-HBF-Kioxia第9代BiCS-1Tb-TLC送样]]
- 7/30 Kioxia 送样第 9 代 BiCS FLASH 1Tb TLC，采用 CBA（CMOS 直接键合阵列）+ OPS 双轴工艺，面向 AI PC/手机等低中容量高性能场景
- 双轴策略：第 9 代走「低成本高投资效率」、第 10 代走「高层堆叠超大容量」；更高密度单 die 为 HBF（8/16 层 NAND 堆叠）容量提升提供底座
- FMS 2026 同期：UFS 5.0（10 GB/s 顺序读）与 NX1 数据中心 NVMe SSD（支持液冷），瞄准 AI/hyperscale

## SanDisk 完成首颗 HBF 芯片流片，推理产品样品推迟至 2027（2026-08-24 归档）
整理自 [[inbox/2026-08-20-HBF-SanDisk首颗HBF芯片流片]]
- 8/13 Investor Day 披露已完成首颗 HBF die 流片（tape-out），路线图显示首个 HBF 推理产品样品定于 2027，标志 HBF 从规范走向硅片落地
- 时间线右移：2025.8 首样品目标 2026 H2 → 2026.8 投资者日更新为「推理产品样品 2027」，新一代存储平台整体延后
- 与 SK hynix 联盟主导标准化，Google/Tenstorrent 已入盟；定位 HBM 与 SSD 之间的推理专用内存层级

## HBF 或成 prefill 芯片 GDDR 替代（2026-08-26 归档）
整理自 [[inbox/2026-08-22-HBF-推理内存分析与GDDR替代]]
- 深度分析（Viks Newsletter）梳理 HBF 作为 HBM 与 SSD 之间新层级的定位：容量 8–16× HBM、尚未量产，SanDisk 样品预计 2026 H2、首个推理结果 2027
- 关键新视角：Rubin CPX 证明 prefill 阶段对内存带宽要求较低 → HBF 有望成为 prefill 计算芯片中 GDDR 的替代
- 开放式问题：HBF 能否与 HBM2e 竞争（作为最先进系统 vs HBM4 的低成本替代）；混合架构——替代 GPU 海岸线上的 HBM 还是与之协同——待验证
- 关联 [[#LLM 推理应用]]

## 来源

- SanDisk 官方博客（2025）：Scaling Beyond the Wall
- HPCwire (2026-08-04)：What is High Bandwidth Flash (HBF)
- IEEE Spectrum：High Bandwidth Flash Unlocks Massive Model Storage
- Chipstrat：High Bandwidth Flash: The Full Report
- Kioxia 新闻稿（2025-08-20）：5TB 64GB/s 原型
- SemiWiki / Blocks & Files：SK Hynix HBM+HBF hybrid（H³ 论文）
- Samsung FMS 2026 新闻稿：zHBM / V10 BV-NAND

## HBF 联盟董事会扩容：Jim Keller 加入（2026-08-28 归档）
整理自 [[inbox/2026-08-24-HBF-JimKeller加入联盟董事会]]
- FMS 2026 复盘：Jim Keller（Tenstorrent CEO）加入 HBF 联盟董事会，与 David Patterson（伯克利）、Raja Koduri（Oxmiq Labs）并列，联盟已含 Google、Tenstorrent
- SK hynix 分层内存框架命名：G0.5（3D 堆叠 DRAM）/ G1.5（HBF）/ G2.5（CXL 内存池），按需组合优化 xPU（呼应 [[#FMS 2026 规格补充：8/16 层堆叠 + 三档带宽 + 分层内存（2026-08-19 归档）]]）
- 路线图右移：FMS 发布 0.7 版规范 → 完整规范 2027 初 → 样品 2028 初；SanDisk 展示 BiCS8/BiCS10 NAND
