# NVIDIA Tensor Core、TMA 与 AI 指令体系

- 整理日期：2026-10-25。
- 定位：面向体系结构与编译器学习的技术笔记，重点是算力、搬运、布局和同步如何配合；不是产品排名或峰值算力汇总。
- 规范基线：[CUDA 13.4.2 归档的 PTX ISA 9.4](https://docs.nvidia.com/cuda/archive/13.4.2/parallel-thread-execution/index.html)。软件源码另按固定 release/commit 引用。
- 来源与时间线：[[2026-10-25-NVIDIA-AI指令-一手来源与时间线]]。
- 关联：[[RISC-V矩阵扩展两年进展-2024-2026]]。
- 标签：#NVIDIA #TensorCore #TMA #PTX #CUDA #GPU #AI编译器 #体系结构

## 先建立完整图景

NVIDIA 的 AI 计算接口应拆成三条相互配合的线：矩阵运算负责算，异步搬运负责供数，完成与可见性机制负责让二者正确重叠。Tensor Core 是硬件执行资源，TMA 是数据搬运能力；`mma`、`wgmma`、`tcgen05` 和 `cp.async.bulk.tensor` 才是阅读 PTX 时会遇到的指令族。把这些统称为“AI 扩展指令集”可以作为研究主题，但不能据此忽略它们处在不同层。

最重要的五个判断：

1. **从 warp MMA 到 WGMMA，再到 tcgen05，改变的是协作粒度、操作数位置和完成机制。** 不只是把一次乘法做得更大。累加器从线程寄存器转向独立 TMEM 后，读出、释放和资源占用也进入 kernel 设计。
2. **TMA 的价值是减少搬运路径里的地址计算与线程/寄存器负担。** 它不执行矩阵乘法，不能凭存在 TMA 就断言 GEMM 加速；小矩阵、非规则访问或 descriptor 准备成本可能改变收益。
3. **异步指令必须读成一个协议。** 发起、记录预期事务、等待数据、发起计算、等待计算、回收 stage 是不同事件。把 barrier、fence、commit 和 wait 互换会导致错误。
4. **低精度能力必须绑定精确目标和变体。** FP8、FP4、块缩放、结构化稀疏涉及输入编码、scale/metadata、累加与舍入；“Blackwell 支持”不是所有 SM target 的统一保证。
5. **软件支持必须一路核验到生成代码。** 高层 API、编译器 lowering、PTX 可接受、实际 cubin、硬件测量与模型端到端收益各是不同证据。

## 名词分层：硬件、ISA、编译器和算法不要混用

| 层次 | 代表对象 | 应当问的问题 |
|---|---|---|
| 硬件架构/产品 | Ampere、Hopper、Blackwell；具体 SM target | 哪一种芯片和精确计算能力，哪些资源/指令合法？ |
| 执行与存储资源 | Tensor Core、TMA、共享内存、TMEM | 算什么、数据在哪里、容量/访问规则是什么？ |
| 虚拟 ISA | PTX 的 mma/wgmma/tcgen05、copy、barrier/fence | 操作数、参与线程、数值与完成语义是什么？ |
| 机器指令 | SASS / cubin | 这个编译配置最后真正生成了什么？ |
| 编程接口 | CUDA、WMMA API、CuTe atom、Triton dot/descriptor | 哪些布局/线程/同步细节由用户控制，哪些由工具生成？ |
| Kernel 与算法 | GEMM、attention、convolution、MoE | 形状、复用、融合与调度如何决定瓶颈？ |

PTX 是有版本和 target 约束的虚拟指令集；不能把一条 PTX 指令机械等同为一条 SASS，或从 PTX 寄存器声明数量直接推断物理寄存器占用。CUDA 的 WMMA API 与 PTX `wmma` 指令也属于不同接口层，虽然名称相似。

与 RISC-V 的对照应立足数据流，而不是给 GPU 指令套上标准扩展名字。RISC-V IME/VME/AME 主要讨论 CPU 架构可见矩阵/向量状态、配置、异常与系统管理；NVIDIA 这里还存在 warp、warpgroup、CTA/cluster 参与和 GPU kernel 生命周期，两者不能直接按“寄存器多少”一一对应。

## 如何阅读本文的版本和证据

本文采用归档文档和固定软件提交，避免 `latest` 页面变化后将未来能力倒填到历史。归档 PTX 文档可以证明指定版本中的语义；target notes 才决定具体指令变体在哪些架构可用；CUDA 版本与驱动安装是否匹配则是另一件事。

文中少量 CUTLASS latest 教程与 CCCL unstable API 链接作为阅读导航，按 2026-10-25 访问快照理解，不以它们证明某 API 的首次发布日期；具体软件版本证据以文末固定提交为准。

本文中的代码或流程为静态理解材料，除非明确另有标注，不是已编译、已运行的完整 kernel。没有在真实 NVIDIA GPU 上执行 benchmark，不把厂商宣称的峰值或官方示例结果改写成本次测量。

## 先分清四种“矩阵操作”

| 接口/指令族 | 程序可见参与范围 | 典型操作数位置 | 首要阅读问题 |
|---|---|---|---|
| CUDA `nvcuda::wmma` / PTX `wmma.*` | warp 集体操作 | 分布式寄存器 fragment | fragment 是什么，怎样正确 load/store？ |
| PTX `mma.sync` | warp 集体操作 | A/B/C/D 在寄存器 | 每个 lane 负责哪些元素？ |
| Hopper `wgmma.mma_async` | 4 个 warp，共 128 线程的 warpgroup | B 在 shared；A 在 shared 或寄存器；累加器在寄存器 | 怎样保护异步读取和累加器访问？ |
| Blackwell SM100 `tcgen05.mma` | MMA 单线程发射；计算可覆盖 1 或 2 CTA | B 在 shared；A 在 shared 或 TMEM；累加器在 TMEM | TMEM 生命周期与异步完成怎样表达？ |

warp/warpgroup collective 不等于每线程独立计算一个完整矩阵。反之，单线程发射也不等于单线程完成算术。`cta_group::2` 描述计算使用的 CTA/TMEM 范围，不是“随便任选两个 block”，更不是要求所有线程都发同一条 MMA。[Warp MMA 教程](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/wmma_programming.html) · [WGMMA 教程](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/wgmma_programming.html) · [tcgen05 教程](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/tcgen05_programming.html)

## 历史底座：Volta 与 Ampere

### Volta：从标量线程到分布式 fragment

Volta 引入 Tensor Core 编程，WMMA 的意义是提供 warp 级矩阵操作，而不是让应用把内部算术单元当作普通标量 ALU。历史阅读必须区分 WMMA 逻辑 tile 与底层 MMA：CuTe 的 Volta 案例用 `SM70_8x8x4_F32F16F16F32_NT`，解释 quadpair 的线程/值布局，而 WMMA 的常见逻辑形状是 16×16×16。不能把这两种形状混为硬件规格矛盾。[CuTe MMA Atom 文档](https://docs.nvidia.com/cutlass/latest/media/docs/cpp/cute/0t_mma_atom.html)

PTX `wmma` 隐藏了部分 fragment 分配；`mma` 则要求调用者按规范显式安排寄存器片段。PTX 6.4 文档已经包含 `mma`，因此“mma.sync 首次出现于 Ampere”不严谨：Ampere 是现代常用形状/类型扩展的重要节点，不是整个 `mma` 家族的起点。[PTX 6.4 固定文档](https://docs.nvidia.com/cuda/archive/10.1/pdf/ptx_isa_6.4.pdf)

### Ampere：类型扩展与供数流水线

Ampere 加入 BF16、TF32、FP64 Tensor Core 路径；常见 FP16/BF16 形状包括 16×8×16。整数/二值路径另有形状，不应拿浮点表直接推导。TF32 保留 FP32 级指数范围但降低有效精度，不能将其理解成“免费且完全等价的 FP32”。[Ampere Tuning Guide 12.8](https://docs.nvidia.com/cuda/archive/12.8.0/ampere-tuning-guide/index.html)

典型 warp MMA 数据流是 global→shared→寄存器片段→MMA→寄存器累加器；shared→寄存器常用 `ldmatrix`，也可以普通 load。`ldmatrix` 的职责是合作式装载/分布，不执行矩阵乘。现代 SM80 FP16 的教学实例是 `(M,N,K)=(16,8,16)`；若 block tile 是 128×128，不代表 ISA 一条指令具有这一形状。[Warp MMA 教程](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/wmma_programming.html)

## Hopper：WGMMA 的异步 warpgroup 语义

WGMMA 的主要转变是 128 线程协作，以及可直接用 shared-memory descriptor 为 Tensor Core 供数。累加器仍在寄存器。SMEM descriptor 不是普通全局地址：它描述布局/步长等硬件读取信息。F16/BF16 与 FP8/INT8 的 major-mode 约束不能互相套用。[WGMMA 教程](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/wgmma_programming.html)

同步协议至少分三件事：
1. 生产者写入 shared 的数据对异步消费者可见。
2. WGMMA 所涉及的寄存器访问按其 fence 规则排序。
3. commit/wait 追踪异步 MMA 的完成，之后才可读结果或回收输入。

`wgmma.fence` 不能替代任意 shared-memory 可见性协议；`wgmma.wait_group` 也不能替代 unrelated TMA 完成等待。PTX 8.0 起的 WGMMA 使用 `sm_90a` 架构专属路径。代表性 FP16 指令形状为 `m64n8k16`，更宽 N 由合法指令形状选择，不是任意整数。[PTX 8.0 固定文档](https://docs.nvidia.com/cuda/archive/12.0.1/pdf/ptx_isa_8.0.pdf)

```text
// 教学伪代码：不是可复制执行的 PTX，不代表完整内存模型。
生产者发起输入搬运
消费者等待该 stage 的数据就绪
按来源执行必要的 proxy / register ordering
warpgroup 全体执行 WGMMA
提交 WGMMA group
在读取累加器、重写相关寄存器或复用输入 stage 前，等待所需 group 完成
```

这里最容易犯的编译器错误是把“发射后代码继续运行”误解成“输入已经可覆盖”。流水线 buffer 生命周期结束点应是最后一个异步消费者完成，而不是最后一条 issue 指令。

## Blackwell SM100：tcgen05 与 TMEM

### 固定版本契约

PTX 8.7 记载 `tcgen05.mma` 首次引入于 PTX **8.6**，`.kind::mxf4nvf4` 在 **8.7** 加入；该版本列出的 target 为 `sm_100a`、`sm_101a`，并非所有 Blackwell 型号通用。SM100 TMEM 描述为每 CTA 128 行×512 列、每格 32 bit，按列动态分配，最小 32 列且列数为 2 的幂。这个容量是该架构的接口描述，不应推成所有未来架构的常量。[PTX 8.7：tcgen05 与 TMEM](https://docs.nvidia.com/cuda/archive/12.8.0/parallel-thread-execution/index.html#tensorcore-5th-generation-family-instructions)

### 数据流与 scope

MMA 单线程发射；A 可来自 SMEM/TMEM，B 来自 SMEM，累加器在 TMEM。TMEM 不等于 shared memory，也不是可以用普通 CUDA 指针随意解引用的 register spill 区。分配、TMEM↔register 传输、MMA 发射具有不同参与规则，不能把“单线程”泛化到所有 `tcgen05.*`。`alloc/dealloc/relinquish_alloc_permit` 是 warp 集体操作，双 CTA 分配需要两个 CTA 各自的规定 warp 参与；`ld/st/wait` 也有 warp 集体参与要求。

CuTe 的 SM100 示例使用 `(128,256,16)` FP16 MMA、`CtaGroup.ONE`；2-CTA 模式另行分片。实际合法形状依赖 kind、累加类型、CTA group、sparsity 与 `.ws` 等条件。选形状应查组合表，不能只记“最大 256”。[tcgen05 编程指南](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/tcgen05_programming.html)

### 需要一并研究的指令族

- `tcgen05.alloc/dealloc/relinquish_alloc_permit`：TMEM 资源生命周期。
- `tcgen05.ld/st/wait`：寄存器与 TMEM 之间的数据访问及其完成要求。
- `tcgen05.cp`：SMEM→TMEM 数据路径，可用于 scale 等。
- `tcgen05.mma` / `.sp`：dense / 结构化 sparse 计算。
- `tcgen05.commit`：通过 mbarrier 跟踪前序 `tcgen05.{mma,cp,shift}` 操作完成；不代替 `ld/st` 的专用等待。
- `tcgen05.fence::before_thread_sync/after_thread_sync`：跨线程同步附近的专用排序。

这些名字是阅读索引，不是暗示每个 kernel 必须逐一调用。所选操作的完整同步模板必须来自目标 PTX 版本。[PTX 9.0 固定档](https://docs.nvidia.com/cuda/archive/13.0.0/parallel-thread-execution/index.html)

```text
// 原创生命周期伪代码；省略详细 barrier phase、集体参与和 fence，禁止直接运行。
分配 TMEM，并将 handle 正确交给使用线程
对每个 K stage：
    等待输入 tile 和所需 scale 就绪
    由规定的发射线程发出 tcgen05 MMA
    登记/等待需要观察的异步完成
    只有在消费者完成后才允许生产者复用该 stage
等待累加器计算完成
以合法的 TMEM→register 集体传输读取结果
等待该传输完成，再进行 epilogue
所有引用结束后释放 TMEM
```

## 低精度不是只增加一个 dtype

Blackwell SM100 的 `kind` 家族包含传统 f16/tf32/i8，以及 f8f6f4、带 block scale 的 mxf8f6f4、mxf4、mxf4nvf4。FP8 有 E4M3/E5M2，FP6 有 E2M3/E3M2，FP4 常见 E2M1。格式名还不能完整定义数值行为：舍入、饱和、scale 类型与粒度都要记录。

MX 的 dense scale block 常为 K 方向 32 元素、UE8M0 scale；NVFP4 路径常为 16 元素、UE4M3 scale。它们不是可无条件互换的“4 bit tensor”。数学上每个输入块先乘对应 scale，再参与归约，不能将分块 scale 简单搬到最终输出后统一乘一个数。[CUTLASS Blackwell 功能文档](https://docs.nvidia.com/cutlass/latest/media/docs/cpp/blackwell_functionality.html)

编译器上的直接含义（分析）：scale tensor 是独立的数据依赖，应参与布局推导、字节计数、prefetch 和生命周期管理。只优化 A/B 的带宽，可能让更小但布局敏感的 scale 成为瓶颈。量化精度验证也必须覆盖 scale 量化误差，而非只比较数据本体舍入。

## 稀疏：不把全部模式粗略叫 2:4

PTX 8.7 的 `tcgen05.mma.sp` 压缩的是 A。典型 f16 模式是 2:4，TF32 是 1:2，mxf4/mxf4nvf4 则涉及按 pair 组织的 4:8。压缩数据与合法 metadata 必须同时提供，不能把 dense 矩阵中写零视作已启用 sparse 路径。[PTX 8.7 稀疏章节](https://docs.nvidia.com/cuda/archive/12.8.0/parallel-thread-execution/index.html#sparse-matrices)

分析：稀疏峰值通常以省略的零项也计入“等效算力”；与 dense 比较时要明确 FLOP 口径。实际收益还包含压缩/剪枝、metadata 搬运和 shape padding 成本。对动态 activation 现压缩现计算，与对静态权重离线压缩，成本结构完全不同。

## 2025—2026 新增观察：固定到 PTX 9.4

CUDA 13.4.2 归档对应 PTX 9.4，其新增列表值得继续跟踪：
- `tcgen05` 的 `UE5M3` scale、`.kind::ti16`、`.collector::b::*`、`.decompress::lut::b`。
- `tcgen05.ld/ld.red` 的 `.spcompress`，以及独立 `spcompress/spdecompress`。
- `tcgen05.alloc/dealloc` 的 `.exclusive`。
- `tcgen05.commit` 与搬运/barrier 家族的 `.multicast::cluster::32b` 等新限定符。

这是 **PTX 版本变化清单**，不是“现有 SM100 全部支持”的声明；每条都还需要核查各自 Target ISA Notes 和运行设备。研究优先级应是新数据流/同步契约，而非只记录助记符数量。[PTX 9.4 新特性，CUDA 13.4.2 归档](https://docs.nvidia.com/cuda/archive/13.4.2/parallel-thread-execution/index.html#ptx-isa-version-9-4)

## 编译器/DSL 应保留的信息

CuTe 用 MMA Operation 描述寄存器签名，`MMA_Traits` 描述逻辑类型、MNK shape、线程映射及 thread-value→矩阵坐标布局，再通过 tile 组合扩展为 kernel。其价值不是隐藏所有硬件细节，而是把细节变成可组合的类型/布局约束。[CuTe MMA Atom](https://docs.nvidia.com/cutlass/latest/media/docs/cpp/cute/0t_mma_atom.html)

以下是据上述契约归纳的 IR 设计建议，属于分析而非 NVIDIA 承诺：

1. **类型维度**：输入/累加类型、packed 表示、block scale、sparsity metadata 不能压成单一 dtype。
2. **形状维度**：分开 problem shape、CTA tile、MMA atom shape、数据传输 shape。
3. **布局维度**：逻辑转置、K/MN major、physical swizzle、寄存器 lane mapping 是不同变换。
4. **执行维度**：保留单线程/warp/warpgroup/CTA-pair scope；合法参与集合属于正确性，不是优化提示。
5. **异步维度**：显式表示 issue、完成 token、proxy ordering 与 buffer release；仅有 SSA 数据依赖不足以证明异步内存安全。
6. **资源维度**：register、SMEM、TMEM 独立预算，再与 cluster occupancy 联合调优。
7. **目标维度**：不能将 `__CUDA_ARCH__ >= 1000` 等粗粒度条件当作所有新指令合法性的证明；维护 ISA/target feature 表。

特别注意 SM120：CuTe 的 warp MMA 教程为其提供 block-scaled FP4 的 `mma.sync` 路线，仍是寄存器片段与 warp-synchronous 模型。这与 SM100 的 TMEM/tcgen05 是两条需要单独 lowering 的路径，不宜用品牌名 Blackwell 统一分发。[Warp MMA 的 SM120 block-scale 章节](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/wmma_programming.html#beyond-simple-dense-mmas)

## 先建立正确分工

TMA 是异步数据搬运设施，不是矩阵乘法本身。Hopper 将它用于 global 与 shared 之间的 1D–5D 传输，也支持 cluster 内不同 SM 的 shared-memory 传输；单线程发起大块传输能够减少寄存器中转与地址生成工作。Tensor Core 在数据就绪后执行 MMA。DSM 是 cluster 内共享内存的可寻址能力，不等同于 TMA multicast；前者可以远程读写，后者把同一输入分发给多个 CTA。

因此性能问题应拆为：地址生成与带宽、共享内存布局、MMA 吞吐、同步/缓冲复用。只把普通 load 替换成 TMA 不保证加速。[Hopper Tuning Guide §1.4.1.2–3](https://docs.nvidia.com/cuda/hopper-tuning-guide/index.html#tensor-memory-accelerator)

## Tensor map：描述“怎么寻址”，不是一张已经展开的矩阵

`CUtensorMap` 是 opaque descriptor，不应手工猜测位域。Host 端通常通过 `cuTensorMapEncodeTiled` 或 `cuTensorMapEncodeIm2col` 编码：基址、维度、字节 strides、访问范围/通道数、遍历步长、interleave、swizzle、OOB 方式等。指令再提供本次起点坐标。

NHWC 的最内层是 C；PTX 坐标/维度按最内到最外写作 `(C,W,H,N)`，不能把 NHWC 字面顺序直接塞给操作数。字节 stride 与 traversal/element stride 是不同概念：前者定位物理存储，后者决定被抽取的逻辑位置。一般 im2col 的 bounding-box lower corner 是相对输入空间起点的偏移，upper corner 是相对输入末端的偏移，并非绝对右下角坐标。具体限制由 API 和目标模式共同决定，编码 API 返回成功也不能替代后续同步正确性检查。[CUDA Driver API：Tensor Map](https://docs.nvidia.com/cuda/archive/13.4.2/cuda-driver-api/cuda_driver_api/group__CUDA__TENSOR__MEMORY.html)

CuTe 的 TMA tensor 是坐标映射抽象，不应等同于普通全局指针 tensor；用其 layout/partition 来生成 TMA 坐标与参与者关系，避免“逻辑 GEMM tile = 一段连续物理字节”的错误假设。[CuTe TMA Tensors](https://docs.nvidia.com/cutlass/latest/media/docs/cpp/cute/0z_tma_tensors.html)

## 普通 im2col：究竟滑的是 OW/OH 还是 R/S？

结论：对一次普通 `.im2col` 搬运，可把 filter offset 看成固定，filter-base 按空间位置推进；映射到卷积即不同 OW/OH 位置取同一 `(r,s)` 偏移及一个 C 子块。不能把 Pixels-per-Column 理解成一次自动遍历整个 R×S 滤波器。

三个量必须分开：
- `tensorCoords` 中 W/H/D：filter-base 的起点；N/C 是对应维度起点。
- `im2colOffsets`：加到 filter-base 上，决定实际输入采样位置。
- `Pixels-per-Column`：NDHW 遍历中的像素数；`Channels-per-Pixel`：每像素的 C 元素数。

bounding box 限定 filter-base 的遍历边界，不指定本次要取的像素数。普通 im2col 的 traversal stride 改变空间步长，不减少 Pixels-per-Column 所指定的数量。出界输入按配置填充。上述语义在 Hopper 时代的归档 PTX 已明确，并非 Blackwell 新增。[PTX 8.4 §5.5.4](https://docs.nvidia.com/cuda/archive/12.4.0/parallel-thread-execution/index.html#tensor-im2col-mode)

### 原创 NHWC 手算案例：跨 OW 行但固定滤波器偏移

采用 cross-correlation 形式：

`Y[n,p,q,k] = sum(r,s,c) X[n,p*U-PH+r*DH,q*V-PW+s*DW,c] * F[k,r,s,c]`

令 X 为 FP16，N=1,H=W=5,C=8，R=S=3，U=V=1，PH=PW=1，DH=DW=1。输出 OH=OW=5。

- descriptor dimensions：`[8,5,5,1]`（C,W,H,N）。
- global byte strides：`[16,80,400]`（W,H,N，C stride 隐含为 2）。
- filter-base：W/H 均从 -1 到 3。
- lower corner：W/H 均 -1；upper corner：W/H 均 -1，因为输入末坐标 4 加 -1 得 3。
- Channels-per-Pixel=8，Pixels-per-Column=8。
- 本次起点：`(c0,w0,h0,n0)=(0,-1,-1,0)`。
- 选 `(r,s)=(1,2)`，所以 PTX offsets 顺序为 `(offW,offH)=(2,1)`。

依次对应的输出位置与实际输入位置：

| j | 输出 (p,q) | filter-base (h,w) | 实际输入 (h,w) |
|---|---|---|---|
| 0 | (0,0) | (-1,-1) | (0,1) |
| 1 | (0,1) | (-1,0) | (0,2) |
| 2 | (0,2) | (-1,1) | (0,3) |
| 3 | (0,3) | (-1,2) | (0,4) |
| 4 | (0,4) | (-1,3) | (0,5)，OOB |
| 5 | (1,0) | (0,-1) | (1,1) |
| 6 | (1,1) | (0,0) | (1,2) |
| 7 | (1,2) | (0,1) | (1,3) |

每行取全部 8 个 channels。逻辑结果是 8×8 个 FP16，即 128 字节；OOB 的那一像素是填充值，不是“跳过”，所以不能按有效 global 读取字节少算 transaction bytes。这里 8 个 pixels 既不是 R×S=9，也不是一个输出像素的整块 3×3×8 patch。

若将 implicit GEMM 定义为 M=N×OH×OW、K=R×S×C，本次取的是 8 个 M 位置、固定 `(r,s)` 的 8 个 K 通道。换下一个 `(r,s)` 必须改变 offsets 或由外层调度生成相应操作；矩阵转置约定可以交换行列叫法，但不会改变采样集合。

### stride 与 dilation 的另一个原创核对例

令 H=W=7，R=S=3，padding=2，convolution stride=2，dilation=2，则 OH=OW=4。filter-base 的坐标序列是 -2,0,2,4；lower=-2，upper=-2（6-2=4）。

取 `(r,s)=(1,2)`，对应 offsets `(offW,offH)=(4,2)`；一次取 6 个 pixels，会采样 `(h,w)`：`(0,2),(0,4),(0,6),(0,8),(2,2),(2,4)`。其中 `(0,8)` OOB。

这给出可检验区别：stride=2 控制相邻输出位置的 filter-base 间距；dilation=2 控制同一输出位置的不同滤波器元素间距，体现在本例 offsets 的倍数关系。不能仅把 descriptor 的 traversal stride 改成 dilation，就声称实现了任意空洞卷积。实际 encoder 的参数范围、layout 与目标约束仍需单独检查。

## `.im2col::w` 不是普通 im2col 的拼写缩写

PTX 8.6 引入 `.im2col::w` 与 `.im2col::w::128`，归档 PTX 8.7 可核对其架构限制。这类模式固定 H/D，改变 W 方向访问规则；不能照搬上面跨 H 行的普通 im2col 示例。

- `.im2col::w` 的主体 pixels 数来自 descriptor；`.im2col::w::128` 固定 128，忽略该字段。
- `im2colInfo` 变为 `(wHalo,wOffset)`，不是普通的 `(offW,offH,...)`。
- `wOffset` 同时平移 W 起点与 W bounding-box 两端。
- `.w` 的 halo 与 `.w::128` 的每 32 元素分组 halo 规则不同；总 shared footprint 和交易字节必须按精确模式确认，不能无条件复用 `P*C*sizeof(T)`。
- 这些模式要求合法 swizzle、并有限定目标；Hopper `.im2col` 可用不代表 Hopper 支持 `.w`。swizzle 等当前约束另见 [PTX9.4 tensor modes](https://docs.nvidia.com/cuda/archive/13.4.2/parallel-thread-execution/index.html#tensor-im2col-mode)。

[PTX 8.7：tensor modes / cp.async.bulk.tensor](https://docs.nvidia.com/cuda/archive/12.8.0/parallel-thread-execution/index.html#data-movement-and-conversion-instructions-cp-async-bulk-tensor)

CUDA 13.4.2 固定归档中的 PTX 9.4 另包含 `.im2col_no_offs::w`、32-bit multicast mask、descriptor overrides 等内容，并标明部分功能需要 `sm_107f`。这是截止日前已发布的软件规范范围，但不能归并到 Hopper/Blackwell 通用基线，也不能仅凭指令表宣称已有对应可用硬件。[PTX 9.4 固定归档，版本说明与 Target ISA Notes](https://docs.nvidia.com/cuda/archive/13.4.2/parallel-thread-execution/index.html)

## 同步必须回答四个不同问题

1. barrier 是否初始化并对使用者可见？
2. TMA 是否已经把这次输入写入 shared？
3. Tensor Core 是否已经读完该 shared stage？
4. 输出 store 是否读完 shared，以及是否已完成 global 写入？

这四个问题不能用一个 `__syncthreads()` 或一个“等 barrier”笼统替代。

### mbarrier：arrival 与 transaction bytes 是两笔账

共享内存 transaction barrier 同时追踪 arrival-count 和未完成 transaction-count。phase 完成需要两者满足完成条件；`arrive.expect_tx` 可以把 arrival 与增加期待字节结合。初始化 count=1 可以表示仅 leader arrive，并不意味着只允许一个 waiter；初始化为 CTA 线程数则必须让对应参与者实际 arrive。[CUDA Asynchronous Barriers](https://docs.nvidia.com/cuda/cuda-programming-guide/04-special-topics/async-barriers.html)

普通 TMA 的 `.complete_tx::bytes` 按字节归还交易账。例如一个 phase 发出 A=4096B、B=8192B 两次 copy，就要登记 12288B，不能登记“2 次”、元素数量，或每个线程重复登记整个 tile。count 太大可能永远不完成；错误提前完成可能让 consumer 读到未就绪数据。

初始 phase parity 为 0；wait-parity(0) 等待这个 phase 完成，不是等待 phase 变回 0。循环复用需要维护每个 stage 的 generation/parity，并保证生产者不能绕过消费者两圈而造成 ABA 式误判。使用 libcu++ arrival token 或成熟 pipeline state 通常比手写全局 XOR 更安全。[libcu++ cuda::barrier](https://nvidia.github.io/cccl/unstable/libcudacxx/extended_api/synchronization_primitives/barrier.html)

### async proxy 与线程同步不是同一维度

普通 shared 写通过 generic proxy；TMA 读写通过 async proxy。典型 store 路径应使所有写者的 shared 更新经 async-proxy fence 发布，并通过线程同步让发起者确认写者完成，再发起 TMA。TMA load 的完成被正确观察后，其结果可供消费者使用。bulk-group 与非 bulk `cp.async` 的 async-group 是不同队列，不能混用 wait 指令。PTX 9.4 对某些 TMA load 还允许用 bulk-group 追踪 tensor map 的读取完成；这不代替输入数据搬运所需的 mbarrier 完成等待。

对于 shared→global，`cp.async.bulk.commit_group` 将当前线程的 bulk 操作提交为组；`cp.async.bulk.wait_group.read 0` 只保证源 shared 已被读取，可用于复用源 buffer，不等于 global 写入已完全结束。需要完整完成时用相应非 `.read` 等待，再建立与目标消费者匹配的同步。[CUDA Asynchronous Data Copies](https://docs.nvidia.com/cuda/cuda-programming-guide/04-special-topics/async-copies.html)

### 动态 tensor map 还有独立 proxy

修改 descriptor 后，不应把普通数据的 `fence.proxy.async` 当作 tensor-map 发布。动态 global-memory descriptor 需要 tensormap proxy 的 release/acquire 协议。跨 CTA 即便 cluster/grid 同步，也不能省略使用方所需的 descriptor acquire；同一 CTA 内可按官方协议在 acquire 后同步共享其效果。静态 `const __grid_constant__` kernel 参数与运行时改写 descriptor 的路径应明确分开。[CUDA 13.1 归档：Usage of a Modified Tensor Map](https://docs.nvidia.com/cuda/archive/13.1.0/cuda-programming-guide/04-special-topics/async-copies.html#usage-of-a-modified-tensor-map)

## 安全性优先的概念伪代码

以下不是可直接编译的 PTX/CUDA；`publish_*`、`wait_*` 明确代表待按目标 API 实现的协议，避免把省略细节的教学代码误当成生产代码。

```text
初始化：
  leader 初始化 full_barrier，arrival_count = 1
  按 mbarrier 与 async proxy 要求发布初始化
  CTA 内参与者同步；所有人知道本轮 phase = 0

每个单缓冲迭代：
  leader:
    full_barrier.arrive_expect_tx(A_bytes + B_bytes)
    TMA_load(A -> shared_A, full_barrier)
    TMA_load(B -> shared_B, full_barrier)

  consumers:
    wait_acquire(full_barrier, 本 stage 的当前 phase)
    发起目标架构的 MMA，遵守该指令参与者与 fence 规则
    等待本轮 MMA 对 shared 输入的访问完成

  所有会复用该 buffer 的参与者同步
  phase ^= 1
```

最后的消费者完成与同步是关键。否则 producer 会在 MMA 尚未读完时覆盖 shared，即使下一次 TMA 与 barrier 自身用法完全正确仍会出错。这个串行保守协议适合作为 correctness baseline，再扩展多 stage。

## 多 stage 与 Tensor Core 的交接

CUTLASS pipeline 把两个方向分开：producer acquire 等待 empty stage，consumer wait 等待 full stage；consumer release 表示该 stage 可以重新写入。`PipelineTmaAsync` 可以让 TMA 自动完成相应的满缓冲通知，所以某些 `producer_commit` 是 no-op；这不表示可以省略 transaction expectation 或 buffer 生命周期管理。[CUTLASS Synchronization primitives](https://docs.nvidia.com/cutlass/latest/media/docs/cpp/pipeline.html)

原创状态机建议：每个 stage 独立追踪 `empty_generation → loading → full_generation → consuming → empty_generation+1`。full 信号由 TMA load completion 产生；empty 信号必须晚于最后一个使用该 stage 的异步 reader。增大 stage 数仅改善重叠机会，也消耗 shared memory，可能降低 occupancy。

Hopper WGMMA：warpgroup 是 128-thread 执行单元；数据 ready、寄存器/指令顺序 fence、WGMMA group commit/wait 是不同层次。`wgmma.fence` 不是 TMA 完成等待；`wgmma.wait_group` 也不负责尚未完成的 TMA。shared stage 的释放必须与真正完成的 WGMMA group 对应，不能在仅发出 `wgmma.mma_async` 后立即 release。[CUTLASS WGMMA Programming Guide](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/wgmma_programming.html)

Blackwell tcgen05：`tcgen05.commit...mbarrier::arrive::one` 在对应异步操作完成时产生一次 arrival，不是按 TMA tile 字节做 complete-tx。它的 group qualifier、发起线程与完成域必须匹配。TMEM 访问还有 `tcgen05.ld/st` 对应等待及跨线程同步 fence 要求，不能用 TMA full barrier 一并代替。[CCCL tcgen05.commit](https://nvidia.github.io/cccl/unstable/libcudacxx/ptx/instructions/tcgen05_commit.html)；[CUTLASS tcgen05 Programming Guide](https://docs.nvidia.com/cutlass/latest/media/docs/pythonDSL/guides/mma/tcgen05_programming.html)

## Multicast/DSM 的额外检查

同一 global tile 被多个 CTA 使用时，multicast 可减少重复搬运；接收方使用对应 CTA-relative shared 地址与 barrier。每个接收方都必须准备好目标存储、初始化与登记相应 barrier，完成前不能退出或复用。不要把“发起 CTA 的 barrier 完成”未经论证地等同于“全 cluster 都完成”。具体 target 的 mask 宽度、地址和 completion 规则必须查对应版本。[CCCL cp.async.bulk](https://nvidia.github.io/cccl/unstable/libcudacxx/ptx/instructions/cp_async_bulk.html)

原创实现审查问题：mask 是否只覆盖活跃 CTA？各 CTA 的共享结构偏移是否一致？每个 barrier 期待的是本 CTA 收到的 bytes 还是误乘了 multicast 接收数？是否需要 cluster 同步来保证远程 shared 生命周期？这个 stage 的全部消费者是否都已完成才回收？

## 软件生态：沿源码找到实际指令，而不止看“支持 GPU”

### 分清四个入口

- **CUDA C++ / PTX**：CUDA 提供线程、共享内存、barrier、tensor map 等编程接口；PTX 给出虚拟指令语义。CUDA API 可以把若干底层步骤封装起来，但必须满足其同步和地址空间契约。
- **CUTLASS C++ / CuTe**：CuTe 的 layout 描述逻辑坐标到数据位置的映射，atom 表示一次硬件操作，tiled copy/MMA 把 atom 组织成线程协作与数据分块。CUTLASS 将 mainloop、epilogue 和调度策略组合成算子。看到一个 atom 类型，只说明存在相应指令封装，尚未证明某个 shape 的完整 kernel 已选到它。
- **CuTe DSL**：用 Python 语法表达同类布局、线程和内存层次，经过编译工具链生成 GPU kernel，不是在 Python 解释器里执行每次矩阵乘法。它能降低模板编程负担，但不会消除 stage 生命周期、共享内存布局和 barrier 正确性问题。
- **Triton / Gluon**：高层 dot/tensor descriptor 和低层显式布局接口是不同抽象层。某个 `tl.dot` 是否变成 warp MMA、WGMMA 或 tcgen05，仍要结合目标、dtype、layout、编译选项与生成代码判断，不能按 API 名称直接断言。

CuTe DSL 的描述采用 [CUTLASS v4.8.1 固定文档](https://github.com/NVIDIA/cutlass/blob/2c85f497547eb441f0fe80bfb0fb2e6f9add1719/media/docs/pythonDSL/overview.rst)。阅读对象是编程模型，不是对所有 kernel 的性能保证。

### 截止日可复核的软件锚点

| 项目 | 已核验版本 | 日期与证据 | 本文使用范围 |
|---|---|---|---|
| CUTLASS | v4.8.1，`2c85f497547eb441f0fe80bfb0fb2e6f9add1719` | [release](https://github.com/NVIDIA/cutlass/releases/tag/v4.8.1)，2026-10-21 发布 | CuTe 指令封装、DSL 模型、最近正确性修复 |
| Triton | v3.8.0，`c01b6774b1865984607d89d89d3a10833de92037` | [release](https://github.com/triton-lang/triton/releases/tag/v3.8.0)，2026-08-28 发布 | descriptor/persistent 教程、backend 变化与限制 |

日期由对应 GitHub release API 的 `published_at` 核验，而不是从搜索结果的抓取日期推断。版本不是“任何旧 CUDA 环境都可直接安装运行”的承诺。

### CUTLASS 中可直接追踪的链条

| 文件 / 层 | 能看见什么 | 阅读时应追问 |
|---|---|---|
| [mma_sm90_gmma.hpp](https://github.com/NVIDIA/cutlass/blob/2c85f497547eb441f0fe80bfb0fb2e6f9add1719/include/cute/arch/mma_sm90_gmma.hpp) | `wgmma.fence`、commit/wait group 与具体 MMA inline PTX | 调用者是否满足整个 warpgroup 的一致参与及 operand 生命周期？ |
| [copy_sm90_tma.hpp](https://github.com/NVIDIA/cutlass/blob/2c85f497547eb441f0fe80bfb0fb2e6f9add1719/include/cute/arch/copy_sm90_tma.hpp) | 1D/2D 等 tensor copy 包装、mbarrier 地址及传输指令 | 预期字节数由哪一层累计，是否包含 A/B 两笔传输？ |
| [mma_sm100_umma.hpp](https://github.com/NVIDIA/cutlass/blob/2c85f497547eb441f0fe80bfb0fb2e6f9add1719/include/cute/arch/mma_sm100_umma.hpp) | SS/TS 操作数路径与 `tcgen05.mma.cta_group::1` 等 | 选择了哪个 kind、M/N 约束、TMEM 地址和 descriptor？ |
| [tmem_allocator_sm100.hpp](https://github.com/NVIDIA/cutlass/blob/2c85f497547eb441f0fe80bfb0fb2e6f9add1719/include/cute/arch/tmem_allocator_sm100.hpp) | 单/双 SM allocator，warp 一致参与要求 | 分配、释放、permit 与 MMA 完成谁负责？ |

例如该版本 `SM100_MMA_F16BF16_SS` 使用共享内存 descriptor 作为 A/B，输出通过 TMEM 地址指定；相应 TS 变体的 A 通过 TMEM 地址传入。**这不是所有 `tcgen05` 子变体的完整形状/数据类型表**，只是从 C++ abstraction 追到 PTX 的可复核实例。构建宏也限制编译目标，不能把头文件存在等同运行时设备支持。

### Triton 已能表达什么，仍不能一概而论什么

[v3.8.0 的 persistent matmul 教程](https://github.com/triton-lang/triton/blob/c01b6774b1865984607d89d89d3a10833de92037/python/tutorials/09-persistent-matmul.py)同时展示普通、persistent、TMA 与 device-side tensor descriptor 路径。它使用 `TensorDescriptor.from_tensor` 或 `tl.make_tensor_descriptor`，以 descriptor load/store 配合 dot，并把 `warp_specialize` 作为配置项。

特别值得注意的是，教程在 Hopper warp specialization 分支关闭 flatten，并跳过某些 host-descriptor 组合。这直接说明“提供 warp_specialize 参数”不等于任意目标和任意 kernel 都能通用开启；应以固定版本里的限制、测试和实际生成代码为准。教程自带矩阵形状、精度和参考比较，适合建立可复现实验起点，本文未运行其 benchmark。

3.8.0 发布说明列出 TMA im2col、Gluon 的 CLC、int8 MMAv5、SM121 block-scaled dot 及多个同步修复。这里的含义是**该版本列出了具体 backend 路径**，不意味着 SM100、SM103、SM120、SM121 的全部 dtype/kind 相同，也不证明任意框架前端已经走到相应 kernel。[发布说明](https://github.com/triton-lang/triton/releases/tag/v3.8.0)

### 近期值得关注的不是只有新吞吐数字

CUTLASS 4.8.1 修复了 device-side TMA descriptor 缩小尺寸时可能发生的非法内存访问，涉及 grouped GEMM 等场景；release 明确将修复放在 `tensormap_replace` 路径，并链接驱动已知问题。这个案例说明 descriptor 的生命周期和更新方式可以成为功能正确性问题，不能只审核 MMA 指令本身。[4.8.1 说明](https://github.com/NVIDIA/cutlass/releases/tag/v4.8.1)

该条仅报告公开修复，不判定任何用户机器受影响，也不根据版本号自动推定所有 CUDA、驱动与第三方包组合都已修好。真正复现时应同时记录 GPU target、驱动、Toolkit、CUTLASS/Triton commit 与 descriptor 更新路径。

## 与 RISC-V IME / VME / AME 的谨慎对照

下面只比较设计问题，不声称 ISA 兼容、实现性能相当，或存在一对一继承关系。RISC-V 规范版本与进度见 [[RISC-V矩阵扩展两年进展-2024-2026]]。

| 问题 | NVIDIA 这里观察到的路线 | RISC-V 研究时对应的提问 |
|---|---|---|
| A/B 从哪里来？ | warp fragment、共享内存 descriptor，部分 tcgen05 路径使用 TMEM | IME/VME 的向量供数与 AME 独立 tile load，布局转换成本在哪里？ |
| C 能驻留多久？ | warp/WGMMA 的寄存器累加与 tcgen05 的 TMEM 累加 | 复用 RVV 寄存器、独立 tile/Acc 能否减少 spill 与重复写回？ |
| 谁发起、谁参与？ | 单 issuer、warp collective、warpgroup collective 分指令规定 | 单线程 CPU 指令与共享矩阵 backend 如何约束发起和完成？ |
| 如何供数？ | cp.async/TMA、descriptor、swizzle 与 multicast | load/store、packing、cache/DMA 是否匹配阵列吞吐？ |
| 何时可读/可覆盖？ | mbarrier、async groups、proxy fence、TMEM 操作各有契约 | 内存模型、异步完成、fence 与上下文释放的关系是什么？ |
| 状态谁管理？ | CTA/cluster/kernel 资源生命周期与 CUDA 执行模型 | Linux signal、抢占、ABI、虚拟化和状态保存如何实现？ |

**架构推论**：大型矩阵执行资源越能独立工作，就越需要显式表达数据就绪、计算完成和资源回收。NVIDIA 的启示是接口必须让软件构建可靠流水，而不是“新增独立累加器必然更快”。同样，GPU 的 descriptor/TMEM 设计不能直接替代通用 CPU 的精确异常、上下文切换和 ABI 设计。

## 按数据生命周期设计一个最小实验

### 第一阶段：先验证功能和指令选择

1. 固定 GPU 型号与 `sm_*` target、驱动、CUDA/PTX 版本、编译器和 CUTLASS/Triton commit。
2. 先用容易核算的 FP16/BF16 GEMM，固定 A/B/C 的逻辑 shape、物理 stride、输入转置与累加类型。
3. 从同步版本起步，再分别增加 async copy、TMA、warp specialization、persistent 调度，避免一次引入全部变量。
4. 检查生成 PTX 与 cubin/SASS，确认预期指令路径被选择，而不是只读源码 API 或 profiler 的宽泛分类。
5. 数值测试覆盖尾块、非整齐 stride、极值、NaN/Inf（按格式契约）、不同 K 和 scale；与明确容差和相同数值口径的参考结果比较。

### 第二阶段：验证并发正确性

检查每个 stage 的四个边界：写入之前是否空闲、计算之前数据是否就绪、覆盖之前计算是否停止读取、释放之前是否还有未完成操作。记录 barrier phase 与字节数，审查 descriptor 更新和 shared-memory proxy 可见性。不能因为“某一次测试结果正确”就省略这些证明。

对于双 CTA、multicast 或 cluster 方案，再检查参与集合、barrier 地址、remote shared memory 生命周期及退出顺序。对于 TMEM，另外检查申请参与者、容量、读出完成与释放条件。sanitizer 可以增加发现错误的机会，但不替代对合法参与和顺序规则的检查。

### 第三阶段：再谈性能

至少同时报告：M/N/K、batch、dtype/累加 dtype、稀疏或 dense、scale 格式、tile/stage、cluster shape、时钟/功耗条件、kernel 与端到端计时范围、warm-up、运行次数和分位数。

把开销拆开：host descriptor 构建/更新、量化与 packing、global→shared copy、矩阵计算、epilogue、同步与 launch。比较时应使用同等数值口径与调优程度的基线；将量化排除在一个方案计时之外、却计入另一个，会改变结论。

可提出但不能未经测量就断言的假设：

- TMA 减少普通线程搬运工作后，producer 的指令或寄存器压力可能下降；也可能被 descriptor、stage 内存或小工作量开销抵消。
- 更多 stage 可以隐藏延迟，也会增加共享内存与同步资源占用，未必提高 occupancy 或吞吐。
- multicast 能降低某些重复全局读取，但收益依赖 A/B 复用方向、cluster 调度与数据形状。
- TMEM 扩大累加器设计空间，也可能把瓶颈转移到 epilogue、读出或非矩阵运算。
- FP4/FP8 改变算术与带宽成本，但量化误差、scale 布局和转换本身必须纳入评估。

## 后续资料应围绕哪些问题积累

1. **PTX 版本差分**：新增哪个 target/kind/shape，是否改变数值语义或只增加编码选项？
2. **搬运与同步勘误**：TMA descriptor、mbarrier transaction count、proxy fence、multicast 与 2CTA 的正确性修复。
3. **软件映射**：CUDA/CUTLASS/CuTe/Triton 的固定 release、相关测试和生成代码；不把 issue 计划当 merged。
4. **低精度完整链条**：量化、scale 生成与布局、矩阵乘加、epilogue、模型精度与端到端吞吐。
5. **新架构资源分工**：TMEM、矩阵引擎、shared-memory 带宽、scalar/SFU 与调度瓶颈是否一起改善。
6. **可重复性**：每条性能结果保留平台、软件栈、shape、精度、计时边界和基线；不能将“TFLOPS 翻倍”直接转成模型速度翻倍。

这是一份关注主题与研究框架，不代表已部署新的定时采集任务。后续更新应保留旧版本锚点，另列新增证据与修订原因。

## CUTLASS 4.8 / PTX ISA 9.4 的 Rubin（SM107）增量（2026-10-31 归档）
整理自 [[inbox/2026-10-28-NVIDIA-Rubin-SM107-CUTLASS48与PTX94]]
- CUTLASS 4.8（10-07）给出 SM107 初版：CuTe C++ 侧 Rubin Tensor Core MMA atom + 4 个 block-scaled 示例；目标名 `sm_107a` / `sm_107f`，SM100 的 GEMM/conv 可直接跑，TMEM 分配器走 Rubin 独占路径
- 硬件预算：TMEM 每 SM 576 列（Blackwell 512）、smem 327KiB、FP8 MMA_K=64 / FP4 MMA_K=128 —— 是「同指令族 + 更大资源预算 + 更宽 K」，不是新增 warp 级指令；吞吐手段落在 MMA_K 变宽与 B collector reuse
- PTX ISA 9.4（CUDA 13.4 U1，13.4.1 于 09-09 发）新增 `cvt` 的 `.pzo`、`tcgen05.commit` 的 `.sync_restrict::shared::read::mma::a`；CUDA 13.4 同步给 sm_107 developer preview
- 口径：docs.nvidia.com 正文抽取被拦，上述出自搜索快照的原始文本片段，PTX 9.4 完整 release note 未逐条核对 —— 引用 target 可用性前须回原表复核

## B300 上 INT8 的四层撤除：规格表不是 kernel（2026-10-31 归档）
整理自 [[inbox/2026-10-28-NVIDIA-B300-INT8四层撤除审计]]
- arXiv 2608.11693 逐层审计 Blackwell Ultra（sm_103a）的 INT8 W8A8，得出一条撤除链：规格表有 → PTX 不暴露 → CUTLASS 不生成 → serving 引擎直接崩
- PTX 层：即便在把 FP4 kinds 扩到 sm_103a 的同一版 PTX 里，也从未暴露 `tcgen05.mma` 的 `.kind::i8`；合法整数 tensor-core 路径只剩老式 warp 级 IMMA
- 库层 / 引擎层：CUTLASS 对 103a 目标显式跳过 INT8 UMMA 生成（FP8 则无条件生成）；vLLM 无 Blackwell INT8 GEMM、模型加载后首次 forward 抛硬错；SGLang 的 AOT INT8 GEMM 停在 Sm90
- 方法学价值：把「硬件能力 / 指令语义 / 库支持 / 真实 kernel」拆四层分别取证 —— 与本文档的版本 + target note 读法一致，也说明 PTX 未暴露就等于该精度在 kernel 层不存在
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI芯片推理硬件2026全景]]
