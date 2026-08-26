# TileLang：组合式 Tiled 编程模型，AI 编译器新竞争者
- 来源: https://arxiv.org/html/2504.17577v2
- 日期: 2026-08-26
- 标签: #AI编译器 #TileLang #Triton #GPU编程

## 摘要
TileLang 提出组合式 tiled 编程模型，用显式 tile 级原语 + 自动推理机制编写硬件感知算子，性能追平甚至超越 Triton，代码更简洁（FlashMLA 内核仅约 70 行）。

## 要点
- MI300X 上相对 Torch 达 129.2×，性能与代码紧凑度均超 Triton，接近手写库 AITER 的 95%
- FlashMLA 内核：H100 上 1075.9× Torch，达手写 FlashMLA 98% 性能，仅约 70 行 Python
- GEMM 在 RTX4090/A100/H100/MI300X 逼近 cuBLAS/rocBLAS；统一 block+thread 范式 + 透明调度
- 相关: [[AI编译器2026格局]]
