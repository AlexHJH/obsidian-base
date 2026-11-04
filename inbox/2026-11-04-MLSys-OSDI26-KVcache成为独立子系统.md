# OSDI'26「KV Cache and Long Context」整场：KV 缓存已经变成独立子系统
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions
- 日期: 2026-07-13（会议）/ 论文已上线
- 标签: #MLSys #OSDI2026 #KVcache #存储层级 #LLM推理

## 摘要
OSDI'26 把 KV cache 单独排成一个 session，五篇里四篇都在解决同一个矛盾：HBM 装不下长上下文 KV，但往下搬（CPU 内存 / SSD）会变成 I/O-bound。四篇的解法分别落在 I/O 布局、稀疏索引可预测性、零拷贝与内存气球上。

## 要点
- **Strata**（Stanford/NVIDIA + SJTU，已合入 SGLang 并上线）：naïve 分层缓存被三类问题拖死——KV 碎片化导致小传输、加载阻塞 prefill、scheduler 忽略 cache-load 延迟与 delay hit（miss 期间同一上下文的并发请求）。GPU 辅助 I/O 解耦 GPU/host 布局 + cache-aware 调度；比 vLLM-LMCache 吞吐高最多 5×、比 TensorRT-LLM 高 3.75×，短上下文不退化
- **ECHO**（SJTU + 华为）：原生稀疏注意力把 KV 长度增长变得更陡。利用 index score 的数值可预测性 + query 顺序性做「无损」intra-query（decode）/ inter-query（prefill）预取，全流水融合 kernel 把 recall 开销压在 indexer 计算之下
- **DirectKV**（UVA）：GH200/GB200 的 NVLink-C2C 上做零拷贝卸载——去掉 GPU staging buffer，kernel 直接访问 CPU 侧 KV，KV 生成与 attention 融合 + warp 级流水；传输量 −50%、GPU 显存 −43%、端到端 1.2×
- **Prism**（UCLA/Berkeley 等）：多模型共服务的「GPU 内存气球」，观察到生产流量是 bursty-group（一组模型同时活跃后整体迁移），用弹性内存统一空间/时间共享
- **Simple Is Better**（SJTU + 阿里）：路由打分只把「KV 感知的新 prefill token 数」× 「实例当前 batch 大小」相乘，超参在比较中自然约掉 → 无需调参即同时兼顾 KV 命中与负载均衡
- 读法：与 [[推理引擎格局-vLLM-SGLang]] 对照——工业引擎在争「一步 decode 多快」，学术侧在争「KV 放在哪一层、谁来调度搬移」；Strata 已进 SGLang 说明这条线正在产品化
- 口径：仅读会议 program 页摘要，未读全文与实验细节
