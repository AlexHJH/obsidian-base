# MLX Metal SDPA 短查询精度回归：2–8 token 时误差涨约 300 倍（仅 NAX）
- 来源: https://github.com/ml-explore/mlx/issues/4808
- 日期: 2026-10-31
- 标签: #MLX #数值精度 #Attention #投机解码 #M5

## 摘要
#4723 引入的 `sdpa_split` 把 33–128 个 stacked rows 的调用切段并行，但 NAX 版本在 2–8 个 query token、≥128 keys 时，输出对 float64 参考的最大绝对误差从 1e-4 涨到 3e-2~4e-2。这个形状恰好是投机解码验证草稿 token 的窗口——#4723 想优化的场景被它自己破坏。issue 仍 open。

## 要点
- 触发条件：rows = qL×(H/Hkv) 落在 33..128 → sdpa_split；≤32 仍走未改动的 sdpa_vector
- 实测（M5 Max、bf16、24 Q heads / 2 KV heads、4 query tokens）：head_dim 64/96/128/192/256 在 kL=1004 均退化到 2.6e-2~4.3e-2；kL=104 未 split 的档仍是 4.1e-4
- 非 NAX 侧（M2 Pro、Python 绑定）同配置仍 1e-4 量级 → 是 NAX kernel 的 bug，不是 split 算法本身错
- 规避：`use_split=False`；维护者 zcbenz 提议改成默认关、只在投机解码处显式开
- 命中面很宽：12 rows/token 的模型 3 个 token 触发，8 KV / 40 Q heads 的模型 7–8 token 触发
- 关联 [[MLX生态2026]]
