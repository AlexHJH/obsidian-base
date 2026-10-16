# keras-mlx 进入 keras-team；Ollama MLX runner 的 KV cache 泄漏修复与实测复核
- 来源: https://github.com/keras-team/keras-mlx ; https://github.com/ml-explore/mlx/discussions/654 ; https://github.com/ollama/ollama/issues/16698 ; https://contracollective.com/blog/ollama-mlx-backend-vs-metal-apple-silicon-benchmark-2026
- 日期: 2026-10（社区讨论）/ 2026-08-29（Ollama issue 收尾）
- 标签: #MLX #AppleSilicon #Keras #KV缓存 #Ollama

## 摘要
Keras 3 的 MLX 后端从第三方 WIP 变成 keras-team 官方仓库 keras-mlx，`KERAS_BACKEND=mlx` 即可训练/推理走 MLX，标志着 Apple Silicon 从「能跑推理」升级为「框架一级公民」。另一侧，Ollama MLX runner 早期 v0.30.x 的 KV cache 泄漏被定位并修复（v0.32.11 起），10 月的第三方复核仍维持「比 Metal 快约 93%」的结论，但把 32GB 内存门槛与模型覆盖面列为硬约束。

## 要点
- keras-mlx（keras-team/keras-mlx，Apache-2.0）：`pip install keras-mlx` + `KERAS_BACKEND=mlx` 切换；测试入口在 keras 仓库内跑 `KERAS_BACKEND=mlx pytest keras`，仓库仍标注「早期、欢迎 bug 报告」
- 意义：Keras 生态（含 tf.keras 老代码、多后端写法）不必再为 Mac 换框架；此前 MLX 侧的定位是 mlx-lm/mlx-vlm 这类推理工具，训练侧缺官方高层 API
- Ollama 侧工程细节：MLX runner 早期版本 KV cache 在多请求间不释放（#16698，RSS 涨到 75GB、tgTPS 崩塌），根因是「boundary snapshot 切的是 forward-sized conv buffer 的切片」，随 #17077 修复、v0.32.11 之后发布
- 实测复核（2026-10 第三方，M4 Max 64GB / Qwen3.5 int4）：Metal 57.8 tok/s 解码、1154 tok/s prefill → MLX 111.4 与 1810；与厂商口径一致
- 硬约束：MLX runner 需 ≥32GB 统一内存（8/16GB 机型仍走 Metal），预览期只覆盖少数模型族；官方结论是「同模型同量化下收益可复现，换模型族别套用」
- 判据：Mac 侧的真实分水岭已从「裸 decode 吞吐」转向「缓存复用策略 + 内存门槛」——oMLX/ANE 缓存、Ollama 快照式 KV 都印证同一方向
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
