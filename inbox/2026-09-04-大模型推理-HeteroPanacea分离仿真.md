# HeteroPanacea：Agentic 推理的分离式服务仿真框架 (arXiv)
- 来源: https://arxiv.org/abs/2608.03741
- 日期: 2026-09-04
- 标签: #大模型推理 #PD分离 #Agentic推理 #异构系统

## 摘要
面向 Agentic 推理的分离式服务仿真框架，从量化、并行调度、PDAF（prefill-decode-attention-FFN）NPU 异构三个维度做系统级仿真，回答「异构系统中每个组件该配什么硬件」这一尚未被探索的问题。

## 要点
- 确认 Prefill-Decode 分离收益：相比传统 GPU 服务吞吐最高 +75%
- 4 路 PDAF 分离（自定义 NPU 假设下）跨模型提升最稳定
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
