# Ollama v0.40 把 MLX 变成 Apple Silicon 默认引擎，回退边界才是新问题
- 来源: https://modelfit.io/blog/ollama-0-40-0-mlx-default-apple-silicon ; https://llmcheck.net/guides/ollama-mlx-mac
- 日期: 2026-09-25
- 标签: #MLX #AppleSilicon #Ollama #推理引擎

## 摘要
v0.40.0（2026-09-25）起，Apple Silicon 上凡是 Ollama MLX 运行时支持的架构都默认走 MLX，其余仍回退 llama.cpp；此前要 `OLLAMA_MLX=1` 才启用。判断实际后端从「猜」变成看 `ollama ps` 的 RUNNER 列。

## 要点
- 默认化推翻 10 月前的观测口径：当时无 per-model 覆盖、`ollama ps` 不显示后端、未覆盖模型静默回退 Metal → 前后对比数字容易被误读成「MLX 没用」
- 同机唯一的公开对比（M4 Max）：MLX 相对 llama.cpp 在 30B MoE 上 +19%，8B 只 +4% —— 收益随模型规模与 MoE 结构放大
- 回退面仍有坑：Nemotron 3.5 Bolt（v0.40.5 起收录）官方标注 Apple Silicon/Metal 不支持，stock llama.cpp 在 prompt 处理阶段 segfault（issue #30610），修复 PR #30699 至 10/18 未合
- 版本细节：v0.40.2 首次运行时后台升级旧模型并保留备份（磁盘会短暂翻倍）；v0.40.6 加 q2_0 GGUF 类型；v0.40.7-rc 于 10/18

## 关联
[[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
