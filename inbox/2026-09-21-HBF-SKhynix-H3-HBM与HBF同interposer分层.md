# SK hynix H³：把 HBF 与 HBM 放同一 interposer 做 KV cache 分层
- 来源: https://semiwiki.com/forum/threads/sk-hynix-proposes-hbm-and-hbf-hybrid-for-llm-inference.24754
- 日期: 2026-03-17
- 标签: #HBF #HBM #KVcache #LLM推理 #SKhynix

## 摘要
SK hynix 在 IEEE 论文中提出 H³：在 GPU 同一 interposer 上同时挂 HBM 与 HBF，让 HBF 充当「大容量只读层」缓存权重与共享预计算 KV cache，HBM 存新生成的 KV 与热数据，从而避免把 KV 甩到本地 NVMe（Nvidia ICMSP 路线）的 PCIe 往返开销。

## 要点
- 关键动因：Llama 4 支持千万级序列长度，KV cache 可达 5.4 TB，「需要几十块 GPU 只为存这些值」；未来 HBM 代际扩容/提带宽的速度赶不上推理时延压力
- HBF 预期优势：容量约为 HBM 的 16x，带宽与 HBM 相当；劣势：访问慢（ns → μs 量级）、写寿命低（约 10 万次擦写）、功耗最高约为 HBM 的 4x
- 因此定位为读密集场景：共享预计算的 KV cache（CAG / cache-augmented generation 天然只读），论文称同卡数下可同时处理更多请求
- 结构：HBM 与 HBF 各带 base die 与控制器，D2D 互联，权重+共享预计算 KV 放 HBF，新生成 KV 放 HBM，靠预取/流水补偿时延
- 与 Sandisk 原始「贴近加速器当内存用」的设想相比，SK hynix 方案是「同封装异构两层」，对封装与软件栈（KV 分层管理）要求更高
- 关联 [[HBF-NAND-AI推理内存2026]] [[2026-09-21-HBF-HotChips2026-OXMIQ-HBF可用性边界]] [[推理引擎格局-vLLM-SGLang]]
