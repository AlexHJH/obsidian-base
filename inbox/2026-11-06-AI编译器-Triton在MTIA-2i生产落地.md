# Triton 跑进 MTIA-2i 生产：非 GPU 编程模型上的首个规模化落地
- 来源: https://arxiv.org/abs/2608.00325
- 日期: 2026-08-12
- 标签: #AI编译器 #Triton #MLIR #定制加速器 #RISC-V

## 摘要
Meta 公开 Triton 在自研推理 ASIC MTIA-2i 上的生产级实现：新写 Triton 后端 + 改造 TorchInductor 代码生成 + 少量语言扩展，性能追平专家手写 C++ kernel。一年内覆盖约 60 类模型、50% 的 layer、47% 的非 GEMM 执行时间。

## 要点
- 编程模型差异（这是核心难点）：MTIA-2i 是向固定功能单元异步下发块级命令，而非 GPU 的线程-指令模型；片上内存要程序员用 FIFO 显式管理，没有硬件 cache 层级；需要手工排命令流水，没有硬件 warp 调度器隐藏延迟
- 编译策略：复用上游 Triton parser 保持语言级兼容，中端把 op 映射到架构（多为 MLIR Vector → RVV 原语），后端串软件流水、CB(片上 buffer) 贪心定容与指针管理、PE 间负载均衡（PID→PE 映射策略）
- 长尾算子走 RISC-V：MTIA-2i 的一颗 RISC-V 核支持 RVV，Triton 编译出的 vector op 直接降到 `vadd_vv_i32m2` 这类 intrinsic，长尾算子不必都塞进 FFU
- 语言扩展只加最少几个：PE ID 查询、跨 PE reduction、`tl.extract_slice` 显式细粒度复用——把编译器推断不出的架构信息交回作者
- 对「DSL→新加速器」的意义：结论是编程模型差异可用结构化 lowering + 少量扩展吸收，且一个季度内 Triton 模型数翻三倍、Triton 内运行时间翻倍

关联：[[AI编译器2026格局]]、[[RISC-V矩阵扩展两年进展-2024-2026]]
