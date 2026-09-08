# Ollama MLX 引擎再提速：融合 Metal 内核 + NVFP4 数据中心导入
- 来源: https://ollama.com/blog/mlx-performance
- 日期: 2026-09-08
- 标签: #MLX #Apple-Silicon #Ollama #NVFP4 #推理

## 摘要
Ollama 官方宣布 MLX 引擎在 Apple Silicon 上达到历史最佳性能：借助 MLX JIT 把多个算子融合进单一 Metal 内核、重构 GPU 采样，整体最高提速 20%；并支持 NVFP4，让为数据中心部署优化的模型可直接导入 Mac 运行。

## 要点
- 性能：多 op 融合为单一 Metal kernel（MLX JIT）+ GPU 采样重构，最高 +20%；NVFP4 比普通 4-bit 量化输出质量更高
- 数据中心↔桌面可移植：为数据中心优化部署的模型可直接导入 Ollama MLX 引擎在 Mac 上跑
- 关联: [[MLX生态2026]]
