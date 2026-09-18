# 原生 MTP 解码（MTPLX / MTP head）在 Mac 上的生态位置
- 来源: https://mtplx.com/faq
- 日期: 2026-09-18
- 标签: #MLX #AppleSilicon #MTP #投机解码 #推理引擎

## 摘要
Qwen3.5/3.6/3.8、Nemotron 等模型自带 MTP（multi-token prediction）头，可直接当 drafter 做精确投机采样，省掉第二个小模型。MTPLX 是把这个能力落地的 Mac 运行时（Apache-2.0，`brew install youssofal/mtplx/mtplx`），声称 4bit 包 2x、8bit 包最高 3x 解码加速。**注意：该页所有性能数字均为厂商自测，需独立复核。**

## 要点
- 时序：MTPLX 4/27 跑通精确投机采样、5/2 发布；mlx-lm 的 MTP PR 5/5 达到同等精确性但**至今未合并**；llama.cpp 5/16 加 MTP。mlx-lm 主线走的是 `--draft-model` 外挂 draft 路线
- 精确性做法：Leviathan–Chen 构造（以 min(1, p/q) 接受，拒绝位置按 (p−q)+ 重采样），任意 temperature 下分布无损
- 2.11（9/4）关键工程点：27B 加 flash-decoding verify kernel（16k +7%、88k +16%）；agent 工具轮次前缀复用——文件写入后只重预填 20 token / 0.12s（旧 3535 token / 3.7s），100k 会话恢复约 2s 而非 5 分钟冷预填
- 生态渗透：oMLX（Lightning MTP）与 mlx-serve 直接采用 MTPLX 的 verify-shape Metal kernel / split-K 量化 matmul / M5 NAX tile
- 落地建议：优先用模型自带 MTP head（免维护 draft 对齐）；外挂 draft 才需要 care 同族配对与 K 值
- 关联: [[MLX生态2026]] / [[2026-09-18-MLX-投机解码M5实测与调参]]
