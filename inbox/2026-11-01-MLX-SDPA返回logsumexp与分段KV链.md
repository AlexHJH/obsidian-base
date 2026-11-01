# MLX 提议给 fused SDPA 返回 logsumexp：分段 KV 链才能精确合并
- 来源: https://github.com/ml-explore/mlx/issues/4693
- 日期: 2026-10-14
- 标签: #MLX #AppleSilicon #KVCache #Attention #Agent推理

## 摘要
把长对话 KV 存成「按轮次链接的分段」而非单一增长 buffer，恢复旧会话/分叉时就不必复制整段历史（50K token 会话单轮峰值少 2–5GB）。代价是 attention 要逐段算再合并，而精确合并需要每段的 logsumexp，`mx.fast.scaled_dot_product_attention` 只返回输出。issue 提议新增 `..._with_lse` 变体返回 `(out, lse)`。

## 要点
- 合并公式精确：`lse = logaddexp(lse1, lse2)`，`out = Σ exp(lse_i − lse) · out_i`，无需重算全量 score
- 融合 kernel 自 #4563（SDPA 的 VJP）起内部已经算了 lse，返回它「无可测开销」：4096×32768 档 81.08ms → 80.87ms
- 对照组：手写 q@k.T + softmax + @v 同档 647ms、额外 40GB 显存 → 分段合并省下的不只是拷贝
- fp16/bf16 输入时要求 out 与 lse 都返回 fp32：若各段先舍到 16 bit 再合并，bf16 有 23–28% 元素与正确舍入不同（lse 用 fp32 时降到 0.9–2.6%）
- 形态与硬件：head_dim 128、32 Q heads / 8 KV heads、M5 Pro；用例是 Qwen3.8-27B + MTPLX 的 agent 长会话
- 状态：open、5 条评论，仍是提案未合入；关联 [[MLX生态2026]]
