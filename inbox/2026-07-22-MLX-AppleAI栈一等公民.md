# MLX 成为 Apple AI 栈一等公民：Foundation Models 框架原生集成
- 来源: https://medium.com/@nuthalapativarun/mlx-is-now-a-first-class-citizen-in-apples-ai-stack-run-any-hugging-face-model-through-foundation-9dfb8dad2191
- 日期: 2026-07-22
- 标签: #MLX #AppleSilicon #FoundationModels #Swift #本地推理

## 摘要
WWDC26 后，Apple 的 Foundation Models 框架新增 MLXLanguageModel 后端，开发者可用统一的 Swift API 调用 Apple 端侧模型、Private Cloud Compute 或任意 HuggingFace MLX 社区模型。MLX 从开源框架升级为 Apple AI 栈的原生一等公民。

## 要点
- LanguageModel 协议统一多后端：系统模型 / Private Cloud Compute / Core AI / MLX
- 指向 `mlx-community/some-model` 即可用相同 Swift 代码运行开源模型
- 结合 M5 Neural Accelerator，MLX 比 llama.cpp 快 30-40%
- Ollama 0.19+ 已底层切换 MLX，LM Studio 提供 GUI，生态成熟
- 64GB MacBook 借助统一内存可运行 24GB RTX 4090 装不下的大模型
