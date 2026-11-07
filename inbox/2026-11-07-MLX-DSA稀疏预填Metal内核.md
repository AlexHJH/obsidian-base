# mlx-lm 把 DSA 稀疏预填补成 Metal 内核：长上下文 prefill 1.3–1.6×
- 来源: https://github.com/ml-explore/mlx-lm/pull/2027
- 日期: 2026-11-05
- 标签: #MLX #Apple-Silicon #稀疏注意力 #DSA #Metal内核

## 摘要
三个 PR 串成一条线：#1997（10/23）让 GLM-5 MoE 的 DSA 预填按 indexer top-k 真做 gather 而非 dense mask 后仍算全部 key，#2013（10/28）把同一优化重构到 DeepSeek-V3.2，#2027（11/05）再把 gather→QKᵀ→softmax→PV 这条「每步落内存」的算子链合成单个 Metal 内核。结构是 threadgroup = 1 query × 16 heads，KV picks 按 256 分块、8 个 simdgroup 分担打分、在线 softmax 经 threadgroup memory 汇总、latent 列再切分累加——同一套代码有 pre-M5（simdgroup_matrix）与 M5+/macOS 26.2+（Metal 4 tensor ops）两版。

## 要点
- 实测（MLX 0.32.3、GLM-5.3-4bit、M5 Ultra 512GB）：prefill 16K 上下文 1.40–1.57×、32K 1.35–1.48×、128K 1.23–1.29×；M3 Ultra 只有 1.18–1.22×（16K）→ 一部分收益来自 M5 张量核路径
- DeepSeek-V3.2 增益低一档（M5 Ultra 16K 1.29–1.35×、128K ~1.09×）→ 稀疏度与 indexer 建模方式决定上限，不能跨模型套用倍数
- decode 各深度实测 1.00×，峰值内存仅降 1–4GB（省的是中间张量写回，不是 KV 本身）
- 正确性口径：32,768 token 上 dense vs patch 的 KLD 0.01245、top-1 一致率 96.10%（比 #2013 的 0.01268 略优）；新增 7 个测试，去掉改动即失败
- 默认 `sparse_prefill_min_context=16384`，短上下文不触发；GLM-5.3 典型 64 头 1024-token chunk 对应 4096 个 threadgroup
- 与同周 mlx 内核 PR 同向：`affine qmv_mma` 3–16 行（#4787，M2 Pro batch-8 decode 70→202 tok/s）、NAX split-K/`affine_qmv_nax`（#4781，FP 格式 M=17–256 快 1.3–2.6×）→ 2026-11 的主线是「低 batch 与稀疏形状」的算子补课
- 口径：作者自测（Claude Code 协助写优化并跑基准）、两机型、未第三方复现；已合 main 但未进 release（最新 tag 仍 mlx-lm v0.32.2 / 10-16）

关联：[[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
