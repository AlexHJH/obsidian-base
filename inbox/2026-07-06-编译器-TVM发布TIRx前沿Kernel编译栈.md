# TIRx: Apache TVM 发布面向前沿 ML Kernel 的开源编译栈
- 来源: https://tvm.apache.org/2026/06/22/tirx
- 日期: 2026-07-06
- 标签: #AI编译器 #TVM #TIRx #ML-Kernel #GPU

## 摘要
Apache TVM 于 2026 年 6 月 22 日发布 TIRx，一个针对前沿 ML Kernel 的硬件原生 DSL 与编译栈。TIRx 面向 GPU 和专用 AI 加速器，支持专家手写、Agent 生成及 Megakernel 三种编程模式，性能超越 cuBLAS。

## 要点
- 基于 TVM 构建，storage-first tensor layout 设计，编译栈极简
- 同时服务三种用户：手写优化 kernel、AI Agent 自动生成、Megakernel 系统
- 跨硬件：GPU + 专用 AI 加速器，设计预留未来硬件扩展空间
- 高抽象层隐藏线程分配/显存搬运/指令选择，低层保留原生控制力
