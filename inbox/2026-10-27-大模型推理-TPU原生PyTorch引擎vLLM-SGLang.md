# TPU 上的原生 PyTorch 推理引擎：vLLM-TorchTPU 与 SGLang-TorchTPU
- 来源: https://developers.googleblog.com/announcing-new-vllm-and-sglang-inference-engines-on-tpu
- 日期: 2026-10-20
- 标签: #LLM推理 #TPU #vLLM #SGLang #KVcache

## 摘要
Google 在 TorchTPU（PyTorch 原生跑在 TPU 上）之上新建了 vLLM 与 SGLang 两个引擎后端，分别与 Inferact、RadixArk 合作开发。思路是"平台插件"：保留各自框架的 worker 模型、调度器、KV cache 管理与 API server，只把算子和层实现换成 Pallas 自定义 kernel，因此模型定义基本复用上游 PyTorch。

## 要点
- KV 搬运统一走开源 C++ 库 TPU Sync：直接读 PyTorch tensor 背后的 device buffer，**无需 host 端 staging copy**，独立线程在 Python 之外跑
- vLLM 通过 KV connector 接口接入；SGLang 把它当作 PD 分离的传输后端和 HiCache 的 I/O 后端
- 能力面已覆盖 agentic 场景全栈：自动前缀缓存（含 hybrid 模型）、KV 分层卸载到 host DRAM / Managed Lustre、MTP/EAGLE-3/DFlash/DSpark/n-gram 投机解码、context parallel、FP8 KV cache、FP8/FP4(MXFP4·NVFP4)/INT8/INT4
- 公开基准走 SemiAnalysis InferenceX + AgentX，宣称 Ironwood（TPU 7x）每百万 token 成本 2.01 美分；已是 preview 客户生产在用
- 对照 [[推理引擎格局-vLLM-SGLang]]：厂商正在把"引擎"变成可插拔平台层，护城河从调度算法转向 KV 搬运与 kernel 库
