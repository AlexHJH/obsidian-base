# Event Tensor：动态 Megakernel 的统一编译抽象 (MLSys'26)
- 来源: https://arxiv.org/abs/2604.13327
- 日期: 2026-09-01
- 标签: #AI编译器 #MLSys #MLIR #Kernel融合

## 摘要
CMU 团队提出 Event Tensor，把 tiled-task 依赖编码为一等张量，统一支持动态 shape 与数据依赖动态性，解决 megakernel 融合难以处理真实负载动态计算的问题。配套 Event Tensor Compiler (ETC) 用静态/动态调度自动生成持久 kernel。

## 要点
- 事件张量作为编译器 IR 一等对象，索引表达式把 task 坐标映射到 event 坐标
- ETC 已并入主流开源系统，SOTA 服务延迟同时大幅降低 warmup 开销
- 关联 [[AI编译器2026格局]]、[[MLSys2026论文精选]]
