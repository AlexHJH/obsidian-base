# Triton 编译器登陆 NVIDIA Blackwell
- 来源: https://developer.nvidia.com/blog/openai-triton-on-nvidia-blackwell-boosts-ai-performance-and-programmability
- 日期: 2026-06-14
- 标签: #AI编译器 #Triton #Blackwell #NVIDIA #GPU

## 摘要
NVIDIA 与 OpenAI 合作使 Triton 编译器全面支持 Blackwell 架构，自动利用新 Tensor Core 和 MXFP8/MXFP4 微缩放格式，在 FP8 GEMM 上带来显著性能提升。

## 要点
- Triton 编译器自动针对 Blackwell Tensor Core 生成优化 PTX
- 支持 OCP MXFP8/MXFP4 微缩放格式，精度-性能权衡更灵活
- FP16/FP8 GEMM 均有可观测加速，无需手写 CUDA
- 标志着 Python-first GPU 编程在最新硬件上的成熟
