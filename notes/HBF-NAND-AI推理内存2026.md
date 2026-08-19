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

## 来源

- SanDisk 官方博客（2025）：Scaling Beyond the Wall
- HPCwire (2026-08-04)：What is High Bandwidth Flash (HBF)
- IEEE Spectrum：High Bandwidth Flash Unlocks Massive Model Storage
- Chipstrat：High Bandwidth Flash: The Full Report
- Kioxia 新闻稿（2025-08-20）：5TB 64GB/s 原型
- SemiWiki / Blocks & Files：SK Hynix HBM+HBF hybrid（H³ 论文）
- Samsung FMS 2026 新闻稿：zHBM / V10 BV-NAND
