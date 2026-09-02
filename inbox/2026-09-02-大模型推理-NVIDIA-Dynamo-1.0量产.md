# NVIDIA Dynamo 1.0 GA：分离式推理框架进入量产
- 来源: https://developer.nvidia.cn/blog/nvidia-dynamo-1-production-ready
- 日期: 2026-09-02
- 标签: #大模型推理 #NVIDIA #分离式推理 #Dynamo

## 摘要
NVIDIA Dynamo 1.0（3/16 GTC 发布）将 prefill/decode 分离式 serving 推向量产：开源分布式推理框架，支持 vLLM/SGLang/TensorRT-LLM，GB200 NVL72 上最高 7× 吞吐。

## 要点
- 分离式 serving：prefill/decode 拆到独立 worker 池，智能路由 + NIXL 跨节点 KV cache 传输
- SemiAnalysis InferenceX 基准：DeepSeek R1（FP4）吞吐最高 7×；Grove API 简化 K8s 部署与拓扑感知调度
- 四大组件：Planner / 智能路由器 / 分布式 KV cache 管理器 / NIXL；7.9k stars，Google Cloud 已发布 recipe
- 相关: [[推理引擎格局-vLLM-SGLang]]
