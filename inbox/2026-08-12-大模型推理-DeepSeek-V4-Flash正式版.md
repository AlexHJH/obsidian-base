# DeepSeek-V4-Flash 正式版公测：轻量 Agent 模型登顶全球调用量
- 来源: https://api-docs.deepseek.com/zh-cn/updates
- 日期: 2026-07-31
- 标签: #大模型推理 #DeepSeek #MoE #Agent #MXFP4

## 摘要
7/31 DeepSeek-V4-Flash 正式版 API 上线公测（V4-Flash-0731），仅 130 亿激活参数的轻量版在 Agent 能力上反超自家 V4-Pro-Preview：DeepSWE 7.3→54.4、Cybergym 翻倍至 76.7，接近 Claude Opus 4.8。周调用量升至全球第一（7.22 万亿 token，环比 +13%），缓存命中价低至 0.02 元/百万 tokens。

## 要点
- 结构尺寸与 preview 一致，仅重做后训练；原生支持 Responses API 并适配 Codex；V4-Pro 正式版将尽快发布
- 推理侧优化：MoE 专家权重与 CSA indexer QK 路径引入 MXFP4 量化感知训练，真 FP4 权重推理，国产芯片可稳定部署
- 意义：Agent 时代「效率为王」，国产模型竞争从拼参数转向拼推理效率与落地成本
