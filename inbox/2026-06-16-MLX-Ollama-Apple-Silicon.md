# Ollama 切换 MLX：Apple Silicon 本地推理进入新纪元
- 来源: https://ollama.com/blog/mlx
- 日期: 2026-06-16
- 标签: #MLX #AppleSilicon #Ollama #本地推理 #NVFP4

## 摘要
Ollama 0.19 将 Apple Silicon 推理后端从 llama.cpp 切换为 Apple 自研 MLX 框架，M5 Max 上 prefill 从 1154→1810 tok/s、decode 从 58→112 tok/s。M5 Neural Accelerator 在首 token 延迟上获得 4.06x 优势。

## 要点
- MLX 利用统一内存架构（UMA），CPU/GPU 零拷贝共享张量，消除 Metal 翻译层开销
- 支持 NVFP4 量化格式，精度与 Q4_K_M 相当但内存带宽更低
- M1 Max 实测 decode 从 3.19→23.39 tok/s（7x），所有 M 系列芯片均受益
- WWDC26 发布 MLX 分布式推理：多台 Mac 集群替代云端推理基础设施
