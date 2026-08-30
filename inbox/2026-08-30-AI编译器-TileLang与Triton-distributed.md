# TileLang 与 Triton-distributed：AI 编译器向 tile 级与分布式演进
- 来源: https://tvm.apache.org/2026/06/22/tirx
- 日期: 2026-08-30
- 标签: #AI编译器 #TileLang #Triton #TIRx

## 摘要
AI 编译器研究进入「分布式 + tile 级」新阶段：北大杨智团队推出 TileLang（显式 tile 原语 + 自动布局/线程推理，主流 GPU 上较 Triton 最高 6×）；字节 Seed 郑思泽推出 Triton-distributed（Triton 扩展，集成 OpenSHMEM 通信原语原生重叠计算/通信）。两者均在 HyperAI「Meet AI Compiler」第 7 期同台展示。

## 要点
- TileLang：分离逻辑 tile 数据流与硬件调度，布局/线程绑定交给编译器自动推理，兼顾控制力与开发效率
- Triton-distributed：首倡分布式 AI 负载原生重叠优化，生成代码性能超手写 CUDA/C++，开发成本显著更低
- Apache TVM 6/22 发布 TIRx（前沿 ML kernel 开源编译器栈），正与 TileLang 社区合作，作为其最小化编译底座
- 相关: [[AI编译器2026格局]]
