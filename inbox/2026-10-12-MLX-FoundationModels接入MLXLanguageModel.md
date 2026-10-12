# MLX 接入 Apple Foundation Models：官方 `MLXLanguageModel` 适配器
- 来源: https://developer.apple.com/videos/play/wwdc2026/339 ; https://github.com/ml-explore/mlx-swift-lm/blob/main/Libraries/MLXFoundationModels/README.md
- 日期: 2026-06-09（WWDC26 Session 339）
- 标签: #MLX #AppleSilicon #FoundationModels #端侧推理

## 摘要
WWDC26 把 Foundation Models 框架从「只能用 Apple 自带端侧模型」打开成任意 LLM 后端（S339），并给出两个官方适配：`CoreAILanguageModel`（跑 ANE）与 `MLXLanguageModel`（跑 Mac GPU + MLX）。后者由 `ml-explore/mlx-swift-lm` 提供，直接加载 mlx-community 的 Hugging Face 模型。

## 要点
- 实现方式是协议级：`MLXLanguageModel` 实现 `FoundationModels.LanguageModel`（对标 `SystemLanguageModel`），生成逻辑放在 `LanguageModelExecutor` → 已有的 `LanguageModelSession` 代码（`@Generable` 结构化输出、tool calling、流式、多轮）零改动换后端
- 上手：`#huggingFaceLanguageModel` 宏自动合成 `weightsLocation`/`load`（HF 下载 + tokenizer 加载），也可用直接 initializer 指向本地模型目录；`availability` 属性暴露 `.available/.downloading/.unavailable`
- 前提：需 macOS/iOS/visionOS 27.0 SDK；`FoundationModelsIntegration` 是默认开启的 SwiftPM trait；首次调用要联网拉权重，之后离线
- 生态影响：把「端侧系统模型 / CoreAI 本地模型 / MLX 的数千 HF 模型 / 云端 provider」统一到同一个 API 面上，MLX 从独立框架变成 Apple AI 栈的一等后端
- 口径：6 月仍是开发者 beta，`respond(to:generating:)`、`streamResponse(to:)` 等签名可能变；框架本身宣称今夏开源，落地前以 Apple 文档为准
- 关联 [[MLX生态2026]]、[[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]
