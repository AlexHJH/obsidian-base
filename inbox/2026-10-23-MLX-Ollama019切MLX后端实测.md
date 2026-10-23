# Ollama 0.19 把 Apple Silicon 底座换成 MLX：decode +93%，但只在 32GB+ 上对 Qwen3.5 生效
- 来源: https://contracollective.com/blog/ollama-mlx-backend-vs-metal-apple-silicon-benchmark-2026
- 日期: 2026-10-04
- 标签: #MLX #Ollama #AppleSilicon #本地推理

## 摘要
Ollama 0.19 在 Apple Silicon 上把推理底座从 Metal 版 llama.cpp 换成 MLX。M4 Max（64GB）跑 Qwen3.5 int4，decode 57.8→111.4 tok/s（+93%）、prefill 1154→1810 tok/s（+57%）。但 MLX runner 仍是 preview：需 32GB+ 统一内存才启用，且只确认加速 Qwen3.5 一个模型族，不支持的模型静默回退 Metal。

## 要点
- 开启方式 `OLLAMA_MLX=1 ollama serve`（0.19+），无 per-model 覆盖，`ollama ps` 也不显示当前后端
- 32GB 硬门槛把 base M1–M4 的 8/16GB 机型整个排除，受众实际只剩 Pro/Max/Ultra
- 静默回退是最大坑：加载 Llama/Mistral 不报错也不告警 → 自测的前后对比数字会被误读成「MLX 没用」
- 补丁清单偏工程：KV cache 复用、prompt 处理中周期性快照、修 snapshot 内存泄漏、修 flash attention 误分配
- 判据：单芯片单模型单量化只是一个数据点；MLX 的相对收益随带宽按代延续，绝对比例不迁移
- 附带数据点：mlx-lm 直跑在 M4 Max 上比 llama.cpp 快约 19%（llmcheck 口径）
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
