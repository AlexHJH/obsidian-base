# SASS 里那个 nvcc 从不生成的控制位：屏障豁免 + 可编程 warp 优先级
- 来源: https://arxiv.org/abs/2611.24437
- 日期: 2026-11-18
- 标签: #NVIDIA #SASS #GPU调度 #微基准 #vLLM

## 摘要
SASS 每条指令都带控制码，其中一个字段在 cuBLAS/cuDNN 里出现 50 万次以上、nvcc 却从不生成。作者用指令级微基准解出它控两个独立机制：EXEMPT（被 pending barrier 阻塞的指令可在等待期提前发射）与 START/TILE/END（warp 在三档硬件优先级间移动），并做成后编译二进制优化器，不改源码不改编译器，覆盖 Volta→Blackwell 六卡四架构。

## 要点
- 收益：代表 kernel 最高 +31.9%；llama.cpp 生产 kernel 最高 +5.4%，vLLM 端到端最高 +3.6%——即厂商库自己调过、开源栈没调的差距量级
- 小 tile 受益更大（`mul_mat_q<Q4_K,32>` +4.7%，128 tile 只 +2.1%）：每次迭代计算越少，barrier stall 占比越高
- 边界条件：MEMBAR 即使打 EXEMPT 也不会提前放行；局部内存 spill（LDL/STL）必须一起打标，否则早到的 warp 卡在第一个 spill——某 kernel 打标指令 40→494 才把收益从 +0.4% 拉到 +2.7%
- 判读：这是「编译器面向硬件调度器留白」的实例；对自研多核 SoC 的编译栈，含义是 barrier 该带「可豁免」标记而非全阻塞，且这个标记要能被调度器与编译器共同理解
- 关联 [[NVIDIA-Tensor-Core-TMA与AI指令体系]] [[Nvidia-GPU微架构演进]]
