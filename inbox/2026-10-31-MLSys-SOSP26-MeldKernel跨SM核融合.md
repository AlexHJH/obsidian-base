# MeldKernel（SOSP'26）：把融合边界从 SM 内推到整颗 GPU

- 来源: https://dl.acm.org/doi/10.1145/3830418.3843857
- 日期: 2026-09-28
- 标签: #SOSP26 #GPU #KernelFusion #DSMEM #MoE #编译

## 摘要
MoE 条件路由与变长 batch 让 kernel 输入 shape 越来越动态，根因被定位为「跨 execution phase 的跨 SM 数据依赖」——编译器只能用 kernel 边界保证正确性，代价是 SM 间相位内失衡 + 中间态绕道 off-chip 内存。Meld 把融合边界从 SM 内推到全 GPU：片上互联做细粒度 SM 协作、相位级依赖拆成子任务级依赖、配 JIT reduction，decode attention 上平均比最优方案快 1.3×。

## 要点
- 根因不是「kernel 太碎」，而是跨 SM 依赖被迫用 kernel 边界表达：边界既放大 intra-phase 负载失衡，又强制中间态走 HBM 往返
- 机制一：借 cluster/DSMEM 片上互联把顺序 kernel 变成流体式跨 SM 流水线；机制二：JIT reduction 做无等待、局部化的状态归约（异步通信）
- 解耦执行模型：计算逻辑声明式描述，硬件映射与 SM 间协作**推迟到 runtime** 按实际 shape 适配——这是绕开「只能静态 shape 调优」的关键
- 数据：decode attention 平均 1.3× over best solutions；主场景为 MoE expert routing、变长 batch
- 同方向对照：FlashFuser 走 cluster 内 DSM 融合（kernel 3.3–4.1×、e2e 1.24×、访存 −58%），Meld 把边界再推到全 GPU 并用 runtime 协作吃掉动态性
- 口径：SOSP'26 共 62/390 录用（15.9%）；数字取自论文摘要，未在本机复现

关联：[[MLSys2026论文精选]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[AI编译器2026格局]]
