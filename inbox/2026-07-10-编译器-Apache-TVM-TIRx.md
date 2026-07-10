# Apache TVM 发布 TIRx：面向前沿 ML Kernel 的开放编译器
- 来源: https://tvm.apache.org/2026/06/22/tirx
- 日期: 2026-07-10
- 标签: #AI编译器 #TVM #TIRx #GPU #ML-Kernel

## 摘要
6 月 22 日，Apache TVM 发布 TIRx——一个硬件原生的 DSL 和编译器，专为机器学习 kernel 设计。首版已支持 Blackwell GPU 上的 GEMM、Attention 等 kernel，提供 pip 安装（apache-tvm==0.25.0）。

## 要点
- 定位：面向"快速演进的 kernel + 快速演进的硬件"之间的编译层，填补高层抽象与手工 CUDA 之间的空白
- 设计理念：保留硬件原生控制（线程分配、内存布局、后端 intrinsic），同时让编译器可见可优化的重复模式
- 三大目标用户：手写高性能 kernel 的专家、Agent 生成 kernel、Megakernel 系统
- 配套 CMU 公开课程讲现代 GPU 编程，已开展社区 kernel 库和 benchmark
- 标志着 TVM 从端到端模型编译向 kernel 级编译的纵深拓展
