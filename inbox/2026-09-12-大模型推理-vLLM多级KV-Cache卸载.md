# vLLM 多级 KV Cache 卸载：主机内存成共享推理层
- 来源: https://vllm.ai/blog (2026-09-10 Tiered KV Cache Offloading in vLLM)
- 日期: 2026-09-10
- 标签: #大模型推理 #KVCache #vLLM #分层缓存

## 摘要
vLLM 把原生 KV 卸载从「GPU→CPU DRAM」单跳扩展为多级：以主机内存为中心，新增文件系统 / 对象存储 / 远程节点为二级层，被淘汰的 KV 从下层重载而非重算。

## 要点
- 新增 TieringManager，把 CPU DRAM 从「终点」变成「中间层」，同时保留 GPU 高带宽访存布局与规范化副本
- 二级层可选 File System API、Object Store（S3）、KV Store 或远程节点（也可走 CPU↔CPU 的 P/D）
- 跨 TP rank 统一：CPU DRAM 中的 KV 一律以 TP rank 1 的规范形式存放，支持跨 TP rank 卸载与共享
- tier 可标 `locality: LOCAL|REMOTE`；llm-d 的 FS backend 已按此把 KV block 落成共享文件系统文件，实现跨副本复用
- 意义：长上下文 / Agent 多轮负载下，缓存容量与命中率取代算力成为吞吐主变量
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
