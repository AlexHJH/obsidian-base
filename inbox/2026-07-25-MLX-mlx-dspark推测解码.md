# mlx-dspark: DeepSeek DSpark 推测解码登陆 Apple Silicon
- 来源: https://github.com/ARahim3/mlx-dspark
- 日期: 2026-07-25
- 标签: #MLX #AppleSilicon #推测解码 #DeepSeek #推理加速

## 摘要
社区将 DeepSeek V4 的 DSpark 推测解码方法移植到 MLX，在 Mac 上实现无损推理加速。Gemma-4 12B 编码场景达 1.6×，Ornith-1.0-9B 数学场景达 2.44×。原生支持 OpenAI 兼容 API、streaming、前缀缓存和连续批处理。

## 要点
- DSpark 是 EAGLE 风格推测解码，主模型验证每个 draft token，输出与原始解码完全一致
- Apple Silicon 上 verify cost 限制加速上限约 2.2×（多 token 验证脱离 MLX 量化 GEMV 快路径）
- 支持 DFlash 块扩散 drafter 作为替代，模型依赖选择（Gemma 用 DFlash，Qwen3 用 DSpark）
- 前缀缓存使长对话 follow-up turn 加速 ~13×，服务器支持 tool calls
