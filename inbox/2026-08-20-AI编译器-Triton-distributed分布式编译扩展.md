# Triton-distributed: 原生 Python 实现分布式计算-通信重叠
- 来源: https://github.com/ByteDance-Seed/Triton-distributed
- 日期: 2026-08-20
- 标签: #AI编译器 #Triton #分布式训练

## 摘要
字节跳动 Seed 团队开源的 Triton 分布式编译扩展，将 OpenSHMEM 通信原语集成进编译流程，让开发者在纯 Python 中同时编写计算与通信，实现细粒度计算-通信重叠，性能比肩甚至超越手写 CUDA/C++。

## 要点
- 集成 OpenSHMEM 原语（底层映射 NVSHMEM/ROCSHMEM），计算与通信统一为单源 Python
- 编译器辅助调度 + kernel 融合，对计算/访存/通信三者做联合优化
- 生成代码完全在 GPU 上执行，消除 host/device 编排复杂度
- 相关论文 TileLink 被 MLSys 2025 接收，已支持 Triton 3.4 + NVSHMEM4py
