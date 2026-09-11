# TileLang 进入工程化阶段：ICLR 2026 Oral + LLVM 后端与调度器
- 来源: https://github.com/tile-ai/tilelang
- 日期: 2026-09-11
- 标签: #AI编译器 #TileLang #TVM #TIRX

## 摘要
TileLang（北大杨智团队，TVM TIR 之上的 tile DSL）论文被 ICLR 2026 接收为 Oral（约 1.18% 比例），并被 DeepSeek V3.2-Exp 官方采用。0.1.11→0.1.14 的更新重点从「性能」转向编译器工程化：LLVM 后端、tile scheduler、pass visualizer、pass diff、编译器 pass 计时与 IKET 剖面。

## 要点
- 性能基线：H100 较 Triton 最高 5×、MI300X 最高 6×；FlashMLA 80 行 Python 达手写 CUTLASS 相当水平
- 后端扩张：v0.1.12 新增 LLVM 后端与 backend registry（走出 GPU-only）；Metal simdgroup GEMM、Turing SM75 MMA、AMD gfx950 MXFP4 陆续落地
- 与 TVM 合流：TIRX 迁移把 TileLang IR 切到 TVM 的 TIRX 表示，印证 TIRX 作为「最小化编译底座」的定位
- 配套：TileLang Puzzles 学习工具、CUDA/LLVM 双路 codegen、TMA gather/scatter 与 cluster 拷贝
- 关联: [[AI编译器2026格局]] [[#Apache TVM 发布 TIRx：硬件原生 ML 内核编译器栈（2026-09-09 归档）]]
