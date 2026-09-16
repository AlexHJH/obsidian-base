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

## SanDisk 搭建 HBF 试点产线：供应链成形（2026-08-30 归档）
整理自 [[inbox/2026-08-26-HBF-SanDisk试点产线供应链成形]]
- TrendForce 援引 ETNews：SanDisk 为 HBF 原型产线搭建供应链，日本或为首选产地，试点产线 2H26 完成、年底投产，商业化 2027，较原路线图提前约半年
- 韩国 JK Materials 完成 HBF 用高性能 KrF 聚合物（堆叠数百层 NAND 的关键材料）
- 复用 HBM 制程生态（TSV、键合），HBM 设备/材料厂商在 HBF 中延续技术优势

## Hot Chips 2026：HBF 带宽 vs 容量之辩（2026-09-01 归档）
整理自 [[inbox/2026-08-28-HBF-HotChips带宽与容量之辩]]
- Tom's Hardware：HBF 以 NAND 堆叠提供 ~14× HBM 容量、但带宽仅 0.6×，解决的是容量瓶颈而非带宽瓶颈
- OCP 开放标准三档：Grade1 256GB/384GB/s、Grade2 512GB/1.536TB/s、Grade3 512GB/3.072TB/s（UCIe 2.0）
- 带宽敏感负载下大容量无法弥补带宽缺口，仅适合带宽不敏感的大容量推理场景
- 375 层 4D NAND、能效 2.5×；SK hynix 提 HBM+HBF 混合架构加速 LLM 推理
- 关联 [[#LLM 推理应用]]、[[#OCP 首版技术规范发布（2026-08-11 归档）]]

## NVIDIA 发布 NVHBM：内存控制器塞进 HBM 底座（2026-09-03 归档）
整理自 [[inbox/2026-08-30-HBF-NVIDIA-NVHBM定制HBM]]
- 8/26 NVIDIA 扩展 NVLink Fusion 推出 NVHBM：自研内存控制器集成进 HBM base die，替代「控制器在 XPU die」架构，面向半定制 XPU（Amazon Annapurna Labs 首个合作）
- 宣称较 HBM4E 带宽 +30%、功耗 -15%，主芯片省 25% die 面积用于计算；预计 2028 Feynman 代 GPU 率先采用
- 定位非 HBM 替代品，而是 NVLink Fusion 半定制客户的新「构建块」；与 HBF 互补——NVHBM 压榨带宽/能效、HBF 主打容量
- 关联 [[AI芯片推理硬件2026全景]]

## 论文：探索 HBF 用于现代 LLM 推理的机会与挑战（2026-09-01 归档）
整理自 [[inbox/2026-09-01-HBF-论文探索HBF用于LLM推理]]
- arXiv 2608.13868 系统评估 HBF 用于 LLM 推理可行性：以低延迟 NAND 替代 HBM 的 DRAM，单堆叠容量提升超 14×（512GB vs HBM4 36GB），借 die/plane 级并行实现接近 HBM4 的读带宽（约 1.6 TB/s/堆叠）
- 动因：Llama 4 Maverick 746GB 远超单 GPU 288GB，容量瓶颈突出；混合配置（1×HBM+7×HBF）容量 3620GB、读带宽 11.2 TB/s，全 HBF（8 堆叠）达 4096GB/12.8 TB/s 追平 HBM4
- 软肋：写带宽仅约 0.11 TB/s（NAND 本质），需写合并/追加写规避耐久与写瓶颈
- 关联 [[#LLM 推理应用]]

## HBF 落地之痛：块级访问与软件栈挑战（2026-09-06 归档）
整理自 [[inbox/2026-09-03-HBF-软件栈与块级访问挑战]]
- Chips and Cheese 分析 Hot Chips 2026 上 HBF 的 ML 负载应用：无法替代 HBM，现实定位是「大而冷」数据集的专用内存层，软件栈挑战巨大
- 访问语义接近裸块设备（类 O_DIRECT），需大块对齐读写，改 1 字节也要整块 64KB 读-改-写回
- 新价值点：用 HBF 大容量在各 GPU 间复制权重，减少跨设备 scatter/gather 通信（DMA 取 flash 比跨设备便宜）
- 关联 [[#LLM 推理应用]]、[[AI芯片推理硬件2026全景]]

## Hot Chips 2026：OXMIQ 冷思考——HBF 是「容量点」而非 HBM 廉价版（2026-09-09 归档）
整理自 [[inbox/2026-09-05-HBF-HotChips2026-OXMIQ容量点定位]]、[[inbox/2026-09-05-HBF-LLM推理系统研究arxiv2608.13868]]
- GPU IP 公司 OXMIQ Labs（Raja Koduri）建模 72-GPU 机架跑 Kimi-K2（1T 参数 FP4）：全 HBM 20.7TB/1584TB/s；全 HBF 容量 14×（294.9TB）但带宽降至 922TB/s；混合 89.3TB/279–1418TB/s
- 结论：HBF 更适合超大但偏冷的数据集，多数负载无法替代 HBM；vLLM 需专门改造（显存分配/数据放置/预取/闪存寿命监控），需 HBF + 加速器 + 推理框架三方协同
- HBF 按 SSD 式大块对齐 DMA 访问；与 [[#HBF 落地之痛：块级访问与软件栈挑战（2026-09-06 归档）]]、[[#论文：探索 HBF 用于现代 LLM 推理的机会与挑战（2026-09-01 归档）]]（arXiv 2608.13868 同源复核）互证

## HBF 定位收敛：三档规格确认 +「HBM for the rack, HBF for the box」（2026-09-09 归档）
整理自 [[inbox/2026-09-09-HBF-OXMIQ-HBM-for-rack-HBF-for-box]]
- 三档规格收敛：Grade1 8-Hi 256/384GB、384GB/s（8GT/s UCIe）；Grade2 512GB、1.536TB/s（16GT/s）；Grade3 512GB、3.072TB/s（32GT/s UCIe 2.0）；读块 64B–4KB、写/页 4KB
- 定位：容量瓶颈场景 HBF 让单卡承载整模型（1 GPU 顶 8 GPU HBM），但带宽瓶颈场景 HBM 的 token 成本更优 →「HBM for the rack, HBF for the box」
- MoE 是天然适配点：Kimi-K3 1.56TB 权重中 1.45TB（93%）为专家权重（写一次、读少量）→ 专家放 HBF、热权重留 HBM
- 纠正误读「廉价内存 ≠ 廉价 token」：并发与生成率上升后 HBF 低带宽成为瓶颈，全 HBM 机架每 token 成本反而更低
- 关联 [[#Hot Chips 2026：OXMIQ 冷思考——HBF 是「容量点」而非 HBM 廉价版（2026-09-09 归档）]]

## FLINT：把 HBF 专做成只读权重容量层（2026-09-14 归档）
整理自 [[inbox/2026-09-11-HBF-FLINT-只读FTL与幻影平面刷新]]
- Oliveira、Onur Mutlu 等（arXiv 2608.25062）放弃通用性，只把 HBF 当只读权重层：burst-buffer 控制器动态合并并流水化 HBF 读以掩藏 μs 级延迟，phantom-plane refresh 把刷新移出加速器可见的关键路径，read-only FTL 只保留「逻辑权重 burst → 物理位置」紧凑映射
- 对症消除既有方案三类采纳障碍：粗粒度静态预取、把 NAND 刷新暴露在推理关键路径、闪存管理未按负载特化
- 耐久：10^7 P/E 投影下可支撑连续推理约 8.0 年
- 关联 [[#HBF 落地之痛：块级访问与软件栈挑战（2026-09-06 归档）]]、[[推理引擎格局-vLLM-SGLang]]

## 全栈实测：HBF 当 SSD 平替做 KV 卸载反而更慢（2026-09-14 归档）
整理自 [[inbox/2026-09-11-HBF-全栈实测HBF当SSD平替反而更慢]]
- 北大团队扩展 TokenSim，用 4 条两小时 Qwen-Bailian 生产 trace + 5 个稠密/MoE 模型 + H100/B200 剖面验证「保留 Mooncake 式 KV 卸载栈、底层 SSD 换成 HBF」：端到端延迟涨 2–5.5×，最大 SLO goodput 掉 1.1–2.7×
- 收益条件被写死：更快远端层只在「读 I/O 为瓶颈 + 读多于写 + 带宽可持续」时划算，而瞬态 KV 三条同时违反；写远多于读使栈在远低于峰值带宽处就撞热限，TLC 层磨损快于被替换的 SSD 池
- HBF 自身延迟几乎不是瓶颈：延迟放大 3.75× 而端到端只变化 <1%；真实代价是占用封装内 near-tier 容量与带宽
- 结论：HBF 有价值但不可 drop-in，需 reuse-aware 放置 + 写预算 + 热协调共同设计
- 关联 [[#FLINT：把 HBF 专做成只读权重容量层（2026-09-14 归档）]]

## HBF 三条路线之争：开放标准 vs 三星 zHBM vs 铠侠 GP1（2026-09-13 归档）
整理自 [[inbox/2026-09-13-HBF-三条路线之争-开放标准vs-zHBM-vs-GP1]]
- NAND Research 拆解 HBF 首版 OCP 规范：8/16 层 NAND 堆叠、单封装最高 512GB（首代 die 256Gb）、三级带宽 0.4–3.0 TB/s、走 UCIe 而非私有 interposer；目标是同等成本换 8–16× 容量（HBM 已约 20–30 美元/GB）
- 路线图：Gen2 读带宽 >2 TB/s / 1TB 堆叠，Gen3 3.2 TB/s / 1.5TB
- 联盟缺口是最大变量：创始成员仅 SK 海力士、闪迪、Google、Tenstorrent；英伟达、AMD、Intel、博通、Marvell、美光、三星、高通、西数均未加入
- 竞品：三星 zHBM 把 HBM 直接堆在加速器上（无公开规范）；铠侠 GP1 走 PCIe 6.0 SSD（1000 万 IOPS），2026 年底送评估样机，早于 HBF 2027 商用但带宽更低
- 落地前提：HBF 必须在 tape-out 阶段设计进加速器封装（无法后装）；H³ 的 2.69× 吞吐/瓦尚无独立验证
- 关联 [[#FLINT：把 HBF 专做成只读权重容量层（2026-09-14 归档）]]、[[AI芯片推理硬件2026全景]]

## 长江存储押注 HBF：Xtacking 混合键合正当口（2026-09-13 归档）
整理自 [[inbox/2026-09-13-HBF-长江存储押注HBF-Xtacking混合键合]]
- 长江存储 2026Q2 NAND 出货量升至全球第三（14%），但营收仅第五，数据中心 eSSD 只占其营收 10%；董事长陈南翔与首席科学家霍宗亮联名在《中国科学》撰文，点名 HBF 是大模型存储主流载体
- 手艺对口：HBF 最依赖高性能混合键合，正是 Xtacking 架构八年积累（267 层量产、300 层以上推进）；三星 2025.2 反向支付混合键合专利费用于其 400 层以上 NAND
- 资本：8/21 科创板 IPO 获受理，拟募 330 亿元（刷科创板纪录，超长鑫 295 亿）；2026Q1 单季净利 333.79 亿元、毛利率 76.77%（周期红利多于护城河）
- 冲出货第一的三个前提：武汉三期产能如期爬坡（20 万 → 50 万片/月）、企业级 SSD 打开客户、HBF 从论文变量产订单；行业背景：2026Q2 eSSD 已占全球 NAND 出货容量 48%（去年同期 26%）
- 关联 [[#HBF 三条路线之争：开放标准 vs 三星 zHBM vs 铠侠 GP1（2026-09-13 归档）]]、[[AI芯片推理硬件2026全景]]
