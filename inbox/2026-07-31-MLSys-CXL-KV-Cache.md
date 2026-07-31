# CXL 如何突破 KV Cache 内存瓶颈
- 来源: https://www.trendforce.com/
- 日期: 2026-07-24
- 标签: #MLSys #LLM推理 #CXL #KV-Cache

## 摘要
TrendForce 分析指出，CXL（Compute Express Link）内存扩展技术正在成为解决大模型推理中 KV Cache 内存瓶颈的关键方案，可实现内存池化和弹性扩展。

## 要点
- KV Cache 随上下文长度线性增长，单机 HBM 容量已成推理瓶颈
- CXL 允许跨节点共享内存池，显著提升长上下文推理的显存效率
- 多家厂商已推出 CXL 内存模块原型，与 GPU/NPU 协同工作
