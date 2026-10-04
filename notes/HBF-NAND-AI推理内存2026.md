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

## HAVEN：把 HBF 当 GPU 封装内向量检索容量层（arXiv 2603.01175，2026-09-15 归档）
整理自 [[inbox/2026-09-15-HBF-HAVEN向量检索近存加速]]
- 瓶颈：IVF-PQ 要高召回必须 rerank（取回全精度向量），十亿级向量库放不进 HBM 只能落 CPU DRAM/SSD，跨设备搬运吃掉延迟与吞吐
- 设计：把 3D NAND 重构为分布式 subarray 并行结构、按功耗约束建模 HBF 带宽，并把 rerank queue 与检索单元放进 HBF 堆栈下方的 logic die
- 结果：相较 GPU-DRAM / GPU-SSD 系统，rerank 吞吐最高 20×、延迟最高 40× 改善（十亿级数据集），使「带 rerank 的高召回」达到此前只有无 rerank 才有的吞吐
- 作者：Po-Kai Hsu、Weihong Xu、Qunyou Liu、Tajana Rosing、Shimeng Yu（Georgia Tech）
- 意义：HBF 价值不止放权重——算术强度低但容量敏感的检索类负载是另一条落点，与 MoE/冷数据定位互补

## SanDisk 首颗 HBF 流片：2027 样品、2028 量产（2026-09-15 归档）
整理自 [[inbox/2026-09-15-HBF-SanDisk首颗HBF流片]]
- 8/13 Investor Day 披露已 tape out 首颗 HBF 产品：样品 2027 年、量产 2028 年
- 高盛：HBF 结合 HBM 级读带宽与 8–16× 容量，模拟显示 4 颗 HBF GPU 的 token 产出 ≈ 8 颗 HBM GPU（即 HBF-only 架构用约一半 GPU 达到 HBM-only 产出），可缓解容量墙、减少 GPU 数量、降低推理基建成本
- Mizuho 更保守：HBF 难直接取代 HBM，现实价值是支撑解耦式（disaggregated）推理架构；联盟成员含 Google、Meta、SK hynix、Tenstorrent，NVIDIA 尚未表态采用
- 规格回顾：OCP 首版规范 8-Hi/16-Hi 堆叠、最高 512GB，Grade 1–3 带宽 0.4–3.0 TB/s（UCIe）
- 商业侧：FY28–FY30 营收中高双位数增长、调整后毛利率维持约 80%（NBM 新模式）；已签 8 家客户（含 3 家美国超大规模云），覆盖 FY27 存储产出约 50%、FY28 约 2/3；定位与「HBM for the rack, HBF for the box」一致

## FlashAccel：把 HBF 接进 GPU 封装，6 堆栈换 2.49× 吞吐（arXiv 2607.10186，2026-09-17 归档）
整理自 [[inbox/2026-09-17-HBF-FlashAccel-HBF集成GPU高吞吐推理]]
- 中科院计算所/微电子所方案：在 HBM GPU 内集成 6 个 HBF 堆栈，靠巨平面/超页数据布局、HBF 感知存储层与异步预取隐藏闪存延迟；100ms SLO 下每 GPU 吞吐 2.49×、能效 1.93×，优于「直接堆两倍 GPU」
- 关键矛盾：HBF 延迟约 4μs（HBM 100ns 的 ~40×），峰值带宽要求跨数百 plane 并发访问，否则有效带宽骤降
- 硬件两路线：CLI（用 HBF 换掉部分 HBM 堆栈）vs CSI（HBM 旁并列加 HBF，扩容为主，实测增益路径）
- 布局/软件：权重按巨页大小轮询散布到巨平面，动态 KV 用「选择性复制」把过载巨平面数据搬回 HBM；基于日志的持久对象存储层免 FTL 元数据开销 + 程序员控制异步预取
- 耐久性与收益：SLC + 巨块级回写，5 年 14.9 万 TB 写入 vs 器件 112.5 万 TBW；全量 KV 常驻使多轮对话重算 token 最多减 89%

## HBF 标准落地与 H3 混合架构：批处理提升 18.8×（2026-09-17 归档）
整理自 [[inbox/2026-09-17-HBF-标准落地与H3混合架构]]
- 定位共识：HBF 是「HBM 的搭子而非替代者」，补的是 KV Cache 容量断层——上下文 64K → 1000 万 token 时 KV 需求从百 GB 跳到 TB 级
- 厂商口径：闪迪称同封装尺寸/堆叠高度/功耗下容量为 HBM4 的 8–16× 且成本相近；SK 海力士仿真称 B200 上 8×HBM3E + 8×HBF 组合每瓦性能达纯 HBM 的 2.69×
- H3 混合架构（SK 海力士 IEEE 论文）：HBM 与 HBF 同中介层级联接入 GPU，HBF 放权重与预计算 KV、HBM 放动态热数据；1000 万 token KV 场景批处理规模提升 18.8×，32 颗 GPU 的工作量可压在 2 颗上
- 规范内容：系统接口/电气要求、xPU-HBF 主机接口、裸晶堆叠可靠性与封装指南，为 HBF 与 HBM 共存预留空间
- 风险：规模化量产预计 2028 年，标准抢跑于样品；多数头部 AI 加速器厂商尚未公开表态
- 关联 [[#FlashAccel：把 HBF 接进 GPU 封装，6 堆栈换 2.49× 吞吐（arXiv 2607.10186，2026-09-17 归档）]]、[[AI芯片推理硬件2026全景]]

## DASH：HBF 双路径直连 GPU，MoE 专家投递吞吐 +1.94×（arXiv 2608.14333，2026-09-19 归档）
整理自 [[inbox/2026-09-19-HBF-DASH-MoE双路径专家投递]]
- KAIST 方案：HBM 与 HBF 同时作为主存层，专家权重既走「GPU↔HBF 直连」也走「HBF→HBM base die→GPU 中转」，两路并发抬聚合投递带宽——不复制权重、也不引入共享中转瓶颈
- 路由粒度：以「整个专家」为单位分配两条路线并并发传输，规避权重复制与单路拥塞
- 延迟隐藏：early expert determination 提前判定将用到的专家，让 HBF 读延迟与前置计算重叠
- 干扰隔离：不可变专家权重与可变 KV Cache 分开管理，避免读写特性不同的两股流量互相拖累
- 结果（事件驱动 continuous-batching 模拟 + 实测 GPU 计算延迟）：相对「全部经 HBM base die 中转」的单路设计，吞吐 1.94×、端到端 1.90×
- 定位差异：FLINT 把 HBF 做成只读权重层、HAVEN 做检索容量层，DASH 瞄准 MoE 稀疏专家投递，把 HBF 从容量扩展升为 GPU 内存系统一等公民
- 关联 [[推理引擎格局-vLLM-SGLang]]

## HBFSim：在真 GPU 上忠实模拟 HBF 的容量/放置决策（arXiv 2609.09800，2026-09-19 归档）
整理自 [[inbox/2026-09-19-HBF-HBFSim-真机模拟HBF时序与热效应]]
- 问题：HBF 规格 2026-08-03 已发布、首批器件 2027 年初才出样，容量与数据放置决策却等不起硅
- 既有手段都不够：存储模拟器只回放访问序列不跑负载；GPU 模拟器不执行真实 kernel；周期精确模拟器跑不完一次推理
- 机制：发射（issue）与消费分离，让真硬件执行的计算去隐藏 HBF 访问延迟；时序取自真机测量而非参数表；改写 PTX 并 gate kernel launch
- 热与保持：结温同时决定 HBF 可持续带宽与保持期限（到期强制 refresh 写），把 NAND 温变特性纳入评估
- 结果：6 个校准点与实测器件完全一致、零危险发射；未改动的 vLLM 0.15.1 服务 Qwen3-30B-A3B 输出 token 与基线一致；快路径 2s vs 详细参考路径 44s（20.8×）
- 意义：补上 H3 / FLINT / FlashAccel 一类 HBF 研究缺的公共评估底座，出样前即可度量容量与放置取舍
- 关联 [[推理引擎格局-vLLM-SGLang]]

## H³ 分层结构与 Hot Chips 可用性边界复核（2026-09-21 归档）
整理自 [[inbox/2026-09-21-HBF-SKhynix-H3-HBM与HBF同interposer分层]]、[[inbox/2026-09-21-HBF-HotChips2026-OXMIQ-HBF可用性边界]]
- H³ 结构复核（SK hynix，IEEE 论文）：HBM 与 HBF 同挂一个 interposer，各带 base die 与控制器、D2D 互联；权重与共享预计算 KV 放 HBF、新生成 KV 放 HBM，靠预取/流水补偿 μs 级延迟，避免把 KV 甩到本地 NVMe（Nvidia ICMSP 路线）的 PCIe 往返
- 容量动因量化：Llama 4 支持千万级序列时 KV 可达 5.4 TB，「需要几十块 GPU 只为存这些值」；HBF 容量约 16× HBM、带宽相当，代价是延迟高约三个数量级、写寿命约 10 万次擦写、功耗最高约 4× HBM → 只做读密集场景（CAG 只读 cache-augmented generation 天然契合）
- OXMIQ 边界复核：HBF 是「高容量内存」而非「廉价 HBM」，只在容量受限时占优；三档规格中仅 Grade 3（512GB / 3.072 TB/s UCIe 2.0）可对标 HBM4E 且上市时间未知
- 成本结论「HBM for the rack, HBF for the box」：72-GPU 机架跑 Kimi-K2（1T FP4）时全 HBF 让单卡装一个模型实例（72 实例/机架 vs 9 实例），省的是「装模型的卡」而非 token 成本；并发与生成率上升后全 HBM 机架单 token 成本更低
- 工程前提：HBF 需 64KB 读 / 1MB 写才能打满带宽，vLLM 需专门支持（显存分配、预取、闪存寿命监控），是硬件厂 + 加速器厂 + 推理框架的三方工程
- 与既有归档判断一致（[[#Hot Chips 2026：OXMIQ 冷思考——HBF 是「容量点」而非 HBM 廉价版（2026-09-09 归档）]]、[[#HBF 定位收敛：三档规格确认 +「HBM for the rack, HBF for the box」（2026-09-09 归档）]]）：本次为原文级复核，未引入新结论
- 关联 [[推理引擎格局-vLLM-SGLang]]

## SK 海力士「New Spectrum」+ 美光近 GPU NAND：分层共识、路线分歧（2026-09-26 归档）
整理自 [[inbox/2026-09-23-HBF-SKhynix-AI-Infra-Summit三元分层]]、[[inbox/2026-09-23-HBF-美光近GPU-NAND换密度保带宽]]
- SK 海力士 AI Infra Summit 2026（9/15–17 圣克拉拉，约 6000 人、较上届翻倍）叙事从「某颗器件更快」转为**按负载维度做内存放置**：HBF 补长上下文容量、PIM/AiMX 补 decode 带宽、SALT-KV 决定 KV 落在哪一层
- SALT-KV（Semantic-Aware Lifecycle Tiering for KV Cache）：按上下文语义切分 KV cache，综合评估「复用价值 vs 存储成本」在 GPU 显存 / CPU DRAM / eSSD 间放置，现场用配自研 eSSD 的服务器实时演示层间迁移——KV 分层从硬件规范落到运行时可观测的软件方案
- HBF 侧仍只有结构模型，无公开平台支持与量产 benchmark；商业闸门依旧是「公开规范 + 平台适配 + 生产数据」
- 美光在研「近 GPU NAND」（DigiTimes 报道）：高耐久 NAND 模块布置到离 GPU 更近处（GPU 封装内或 PCB 上），让 GPU 直接访问数百 GB NAND 池，工作方式类比 HBM/GDDR7；与 TLC/QLC 追密度相反，主动降低存储密度换取 I/O 速度与读延迟，定位显存与普通存储之间的高速缓冲层
- 路线分歧：闪迪/SK 海力士的 HBF 走 OCP 开放标准 + UCIe（最高 512GB、约 0.4–3.0 TB/s），美光走自研器件 + 近封装集成且未加入 HBF 联盟；与三星 zNAND-O/zHBM、铠侠 GP1 并列 →「HBM 之后必有分层」已是全行业共识，分歧在接口形态与是否开放
- 待观察：美光耐久性指标未公开、是否真进封装而非仅板载、与 HBM 的地址空间如何统一（决定软件栈改造量）

## HBF 标准落地 vs 0.6× 带宽冷水：OCP 首版规范与 Hot Chips 结论并列（2026-09-25 归档）
整理自 [[inbox/2026-09-25-HBF-OCP首个HBF标准规范]]、[[inbox/2026-09-25-HBF-HotChips2026-HBF难替代HBM]]
- OCP 首版 HBF 技术规范（FMS 2026，8/3）：8 层与 16 层 NAND 堆叠两种形态、最高 512GB，带宽分 Grade 1–3（约 0.4–3.0TB/s），以 UCIe 连接多类处理器；生态已有 Google、Tenstorrent；样品 2026 下半年、首批带 HBF 的 AI 推理设备 2027 年初出样
- Hot Chips 2026（8/23）冷水（chipsandcheese + OXMIQ 仿真）：HBF 提供约 14× 容量，但聚合带宽只有 HBM 的 0.6×，负载转成带宽敏感型后大容量无法弥补带宽赤字
- 形态与编程模型：外观像 HBM 立方体、内部是 NAND，数据要靠 DMA 在 HBF 与 DRAM 间搬运且必须大块（64KB 读 / 1MB 写）对齐；host 侧还要接管写均衡等 SSD 控制器职责，编程模型接近块设备而非可寻址内存池
- 分歧本质是负载画像：权重读取/冷数据集分层有收益（Sandisk 仿真：Llama 3.1 405B 只比「无限容量 HBM」差 2.2%），高并发 decode 这类带宽饥饿型负载用 HBF 反而更差——双方并不矛盾
- 判据：标准先行、产品在后，真实收益必须等 2027 年设备出来才能验证；与 [[#Hot Chips 2026：OXMIQ 冷思考——HBF 是「容量点」而非 HBM 廉价版（2026-09-09 归档）]] 结论一致
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI芯片推理硬件2026全景]]

## FlashAccel 与 H³ 论文原文复核：口径与代价项（2026-09-27 归档）
整理自 [[inbox/2026-09-27-HBF-FlashAccel-软硬件协同HBF加速器]]、[[inbox/2026-09-27-HBF-海力士H3-HBM与HBF混合架构]]
- FlashAccel（arXiv 2607.10186）原文口径核对：摘要为 **2.49× 单卡吞吐 / 1.93× 能效**（100ms 延迟约束，GPU 封装内并挂 6 个 HBF stack）——与 [[#FlashAccel：把 HBF 接进 GPU 封装，6 堆栈换 2.49× 吞吐（arXiv 2607.10186，2026-09-17 归档）]] 一致；一份 09-27 笔记误记为 2.54×，此处以原文为准
- 三条机制：架构级延迟缓解（μs 级长尾必须靠预取/流水隐藏，否则扩容反而拖慢 GPU scheduler）、权重与 KV 各自的专用数据布局提带宽利用率、HBF-aware 存储管理层 + 编程模型统一管理异构内存
- 分工边界：只读的权重与 KV 放 HBF，写密集/延迟敏感数据仍留 HBM；HBF 语义接近「近计算块设备」——按页/大块对齐访问，改一字节要读-改-写整块，与 DRAM 字节寻址不兼容
- H³（SK hynix，IEEE）：原文级复核与 [[#H³ 分层结构与 Hot Chips 可用性边界复核（2026-09-21 归档）]] 一致——同 interposer 上 HBM/HBF 各带 base die 与 D2D 互联，只读层落 HBF；单 stack 512GB、带宽 1.6–3.2TB/s，代价为 μs 级延迟、约 10⁵ 次写寿命、功耗最高 4× HBM

## 09-29 两条 HBF 采集为旧条目重述，无新事实（2026-09-29 归档）
整理自 [[inbox/2026-09-29-HBF-Sandisk首颗HBF-die流片]]、[[inbox/2026-09-29-HBF-美光近GPU-NAND]]
- Sandisk tape-out 条目（TrendForce 8/14 转述 Investor Day 8/13）与 [[#SanDisk 首颗 HBF 流片：2027 样品、2028 量产（2026-09-15 归档）]]、[[#SanDisk 完成首颗 HBF 芯片流片，推理产品样品推迟至 2027（2026-08-24 归档）]] 同源，仍指向「样品 2027、量产 2028」，未引入新事实
- 美光 near-GPU NAND 条目（DigiTimes 9/2 首发）与 [[#SK 海力士「New Spectrum」+ 美光近 GPU NAND：分层共识、路线分歧（2026-09-26 归档）]] 同源，新增细节仅「封装内或 PCB 上、数百 GB 级、工作方式类比 HBM/GDDR7、牺牲密度换 I/O 速度」
- 结论与待观察项均未变：分歧在接口形态与是否开放；美光耐久指标、是否真进封装、与 HBM 的地址空间如何统一（决定软件栈改造量）仍无公开答案

## HBF 进 LLM 服务的分层与写路径核算（2026-10-01 归档）
整理自 [[inbox/2026-10-01-HBF-HotChips2026-64KB粒度与写入约束]]、[[inbox/2026-10-01-HBF-LLM服务分层H3混合与写路径开销]]
- OXMIQ Labs（Hot Chips 2026）给 HBF 划边界：只在「容量受限」时划算，带宽/吞吐受限时反而有害；要求 64KB 读 / 1MB 写、经 DMA 搬运不进 cache 层级，85°C 通电保持约 24h、写寿命由主机侧负责 → 数据放置变成软件问题
- 最佳落点仍是 MoE：Kimi-K3 权重 1.56TB 中 1.45TB（93%）为专家权重（写一次、低频读）；反例是「HBM 当热专家缓存」只在低 batch 或相似查询被批到一起时才划算
- H³（SK hynix + KAIST，IEEE CAL）混合实测：相比「Blackwell + 8×HBM 无 HBF」提升 2.69× 性能/瓦；10M token KV 场景并发批 18.8×，原需 32 张 GPU 的负载可缩到 2 张；CAG 只读场景是 HBF 最佳匹配（Llama 4 支持 10M 序列，5.4TB 缓存）
- 写侧不可忽略：HBF 上 KV 写入占执行时间 5–13.9%（Llama4），上下文越短、GPU 越多、MoE 影响越大；「HBF Sucks!」全栈刻画（arXiv 2608.11668）显示把 HBF 当 KV 场景 SSD 直接替代会抬高端到端延迟并加速磨损
- 判据：HBF 价值须按「访问组织 + 冷热分层 + 写配额」联合评估，单看容量/带宽数字会得出相反结论
