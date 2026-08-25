# vLLM 首届大会：State of vLLM 2026 路线图
- 来源: https://vllm.ai/events/vllm-conference/2026
- 日期: 2026-08-25
- 标签: #大模型推理 #vLLM #推理引擎 #投机解码

## 摘要
首届 vLLM Conference 8/24–26 与 Ray Summit 同场（旧金山）召开，Woosuk Kwon 主讲 State of vLLM 2026：路线图聚焦 Flat Model / Model Runner V2 迁移、多层 KV 卸载的分离式 serving、向 1000+ TPS 冲刺的投机解码与量化 KV 压缩。

## 要点
- 分离式 serving 核心：多级 KV offloading（显存→CPU→SSD）成新方向
- 投机解码目标 1000+ TPS，量化 KV cache 压缩走向生产级
- NVIDIA 专场：FlashInfer kernel + Dynamo 分布式全栈优化 DeepSeek/MiniMax
- Intel 专场：MXFP8/MXFP4 亚 8-bit 格式 + prefill/decode 解耦到专用节点
- 四大赛道：硬件后端、agentic serving、训练、生产推理
