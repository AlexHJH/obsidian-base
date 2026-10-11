# Tutti：把 CPU 移出关键路径，用 GPU 发起的 io_uring 做 SSD KV 缓存
- 来源: https://arxiv.org/abs/2605.03375 ; https://www.alphaxiv.org/abs/2605.03375
- 日期: 2026-05（arXiv 2605.03375）
- 标签: #大模型推理 #KVCache #SSD #io_uring #DMA

## 摘要
长上下文把 KV cache 推往 NVMe SSD，但 GPU 显存布局碎片化会产生海量微小随机 I/O；即使走 GPU Direct Storage，每个 I/O 仍要 CPU 发起，低并行度 CPU 成为瓶颈。Tutti 改以 GPU 为中心的 KV 对象存储打通 HBM↔SSD，CPU 只负责每层异步加载一次 I/O kernel。

## 要点
- 机制：GPU 原生对象抽象 + gio_uring（提交/完成环驻留 GPU，批量异步提交并记录完成）+ 依赖事件保证按数据依赖启动 I/O
- 调度：离线分析各层空闲窗口做查表调度；prefill 读优先、写延后到空窗（含 decode 阶段），避免 I/O kernel 抢占推理所需 SM
- 结果口径：受控固定 32K prompt 实验里 GPU 停顿近零、延迟显著低于现有缓存层；跨节点路径仍走 CPU + RDMA（作者列为 future work 的 GPU-initiated RDMA）
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
