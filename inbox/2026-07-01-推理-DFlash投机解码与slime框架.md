# DFlash 投机解码：并行扩散草稿模型实现 3-15× 推理加速
- 来源: https://www.lmsys.org/blog/2026-06-15-next-generation-speculative-decoding-dflash-v2
- 日期: 2026-07-01
- 标签: #推理 #投机解码 #DFlash #SGLang #Blackwell

## 摘要
LMSYS 联合 Z Lab/Modal 发布 DFlash，用轻量扩散模型并行生成候选 token 块（替代 EAGLE 串行自回归），SGLang 已集成。NVIDIA 在 Blackwell 上实现最高 15× 吞吐提升。

## 要点
- DFlash 核心：扩散模型 + KV injection 并行起草整块 token，克服 EAGLE/MTP 的串行瓶颈
- SGLang 已上线 DFlash，配合 Spec V2 消除 host 开销；Baseten 实测 Qwen3-8B 吞吐 654 TPS（3× vs 基线）
- NVIDIA TensorRT-LLM 集成 DFlash，Blackwell 上 gpt-oss-120b 达 5.8× 吞吐
- 智谱开源 slime RL 框架（6/20）：Megatron+SGLang 原生透传，GLM-5.2 后训练仅用 2 天
- 关联 [[推理引擎格局-vLLM-SGLang]]
