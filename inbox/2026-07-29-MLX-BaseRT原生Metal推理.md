# BaseRT: 原生Metal推理引擎刷新Apple Silicon记录
- 来源: https://arxiv.org/abs/2607.00501
- 日期: 2026-07-29
- 标签: #MLX #Apple-Silicon #LLM推理 #Metal

## 摘要
BaseRT 是2026年7月发布的原生Metal推理运行时，绕过MLX框架层直接调用Metal，通过芯片级kernel fusion和统一内存感知调度，在M4 Pro上实现迄今最高的Apple Silicon LLM推理吞吐。

## 要点
- 对比mlx-lm (v0.31.2) 和 llama.cpp (2026年6月版)，BaseRT在decode吞吐上显著领先
- 核心优势：MLX作为通用数组框架有惰性求值开销，BaseRT直接编写Metal kernel，消除框架层损耗
- 适用场景：对延迟敏感的本地推理，追求极致token/s
- 注意：这是研究原型，尚非生产级框架；MLX生态仍然是首选
