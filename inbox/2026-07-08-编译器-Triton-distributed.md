# Triton-distributed: 字节跳动推出分布式 Triton 编译器
- 来源: https://arxiv.org/abs/2504.19442
- 日期: 2026-07-08
- 标签: #AI编译器 #Triton #分布式训练 #ByteDance

## 摘要
字节跳动开源 Triton-distributed，首个原生支持计算-通信重叠优化的分布式编译器。基于 OpenAI Triton 扩展，支持单节点/多节点 compute-communication overlapping，64 设备规模下性能媲美手写优化库 (Distributed-GEMM, FLUX)，已适配 AMD ROCm CDNA3。

## 要点
- **核心创新**: 编译器级别处理 computation/communication overlapping，无需手工编排
- **编程接口**: `triton.distributed.language` 提供高层/低层原语，与核心 Triton 完全可组合
- **跨平台**: 支持 NVIDIA H800 和 AMD CDNA3，ROCm 博客已有适配教程
- **开源**: GitHub ByteDance-Seed/Triton-distributed，Apache 2.0 许可
