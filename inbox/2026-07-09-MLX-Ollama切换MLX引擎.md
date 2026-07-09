# MLX vs llama.cpp：Apple Silicon 推理引擎格局重塑
- 来源: https://yage.ai/share/mlx-apple-silicon-en-20260331.html
- 日期: 2026-07-09
- 标签: #MLX #AppleSilicon #LLM推理 #M5 #Ollama

## 摘要
2026年3月Ollama宣布切换至MLX作为Apple Silicon推理后端，标志着MLX生态成熟度质的飞跃。MLX利用Apple Silicon统一内存架构实现零拷贝传输，M5的Neural Accelerators在首token延迟上领先llama.cpp的Metal后端4.06倍。

## 要点
- **Ollama 0.19集成MLX**：prefill从1154→1810 tok/s，decode从58→112 tok/s (M5 Max, Qwen3.5-35B-A3B, NVFP4)
- **M5 Neural Accelerators**：专用矩阵乘法单元，MLX率先适配，首token延迟优势4.06x
- **统一内存零拷贝**：CPU/GPU共享物理内存，tensor操作无需显式传输
- **小模型优势明显**：14B以下MLX领先20-87%，27B以上因内存带宽瓶颈与llama.cpp趋同
- **生态局限**：模型覆盖不如llama.cpp全面，社区工具链仍在追赶
