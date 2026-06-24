# MLX × M5：Neural Accelerators 重塑本地 LLM 推理
- 来源: https://machinelearning.apple.com/research/exploring-llms-mlx-m5
- 日期: 2026-06-18
- 标签: #MLX #AppleSilicon #本地推理 #M5 #NeuralAccelerators

## 摘要
M5 在每个 GPU 核心内集成 Neural Accelerators（专用矩阵乘法单元），MLX 在最新 macOS beta 中已原生支持。在 MacBook Pro M5（24GB）上对比 M4，LLM 推理速度提升 19–27%，首 Token 延迟（TTFT）最高提升约 4 倍，源于更高内存带宽与专用矩阵硬件的协同。

## 要点
- M5 Neural Accelerators 内置于每个 GPU 核心（M5 Max 40 核 = 40 个加速器），专攻矩阵乘法
- token 生成受内存带宽制约（M5 Max 达 614GB/s），prefill 受算力制约
- LLM 推理正成为「硬件-软件协同设计」问题，Apple 垂直整合（芯片+Metal+MLX）优势凸显
- 与 [[MLX生态2026]] 的多机分布式推理互补，本地大模型落地加速
