# OSDI'26: Strata——长上下文 LLM 服务的分层缓存框架
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions
- 日期: 2026-08-12
- 标签: #MLSys #OSDI #LLM推理 #KV缓存

## 摘要
OSDI'26（7月 Seattle）上 Stanford/NVIDIA 联合提出 Strata，面向长上下文 LLM 服务的分层上下文缓存框架。论文发现朴素缓存设计常沦为 I/O 瓶颈：KV 碎片化导致小传输浪费带宽、缓存加载阻塞 prefill、调度器忽视加载延迟造成延迟命中。

## 要点
- GPU 辅助 I/O 机制：解耦 GPU 与主机布局，实现大块高效传输
- 缓存感知调度：缓解延迟命中、平衡 batch 隐藏缓存加载延迟
- 已集成 SGLang 并上线生产，吞吐最高达 vLLM-LMCache 的 5×、TensorRT-LLM 的 3.75×

相关: [[HBF-NAND-AI推理内存2026]] [[推理引擎格局-vLLM-SGLang]] [[MLSys2026论文精选]]
