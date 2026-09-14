# mlx-tsfm：TimesFM 3.0 原生移植 Apple Silicon，MLX 跳出 LLM 圈
- 来源: https://github.com/rachittshah/mlx-tsfm
- 日期: 2026-09-14
- 标签: #MLX #AppleSilicon #时间序列 #基础模型

## 摘要
开发者把 Google 时序基础模型 TimesFM 3.0（330M）原生移植到 MLX，445 个 checkpoint 张量一一对应，`decode()` 与 PyTorch 参考实现最大绝对误差约 1e-6（horizon 24–512，含 cpm_revin_refine 的多 patch 路径）。这是 MLX 生态从 LLM/VLM 向「非语言基础模型」扩张的信号。

## 要点
- M4 Max 实测：单次预测约 20ms，批处理吞吐约 475 forecasts/s
- mlx-tsfm 定位为 model-agnostic 的时序推理层，后续可接入其他 TSFM
- 意义：Mac 本地做运维/能耗/行情类时序预测，不再必须挂一个 CUDA 侧推理服务
- 关联: [[MLX生态2026]]
