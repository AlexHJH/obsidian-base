# mlx-serve v26.9.x：投机解码全模型化 + 100 万 token 上下文
- 来源: https://mlxserve.com , https://www.promptquorum.com/power-local-llm/mlx-serve-review
- 日期: 2026-09-16（v26.9.3）
- 标签: #MLX #Apple-Silicon #投机解码 #本地推理

## 摘要
Mac 本地推理服务 mlx-serve 9 月连发三版，把长上下文与投机解码从「少数模型特例」推成默认能力，并把卖点从「跑得动」换成「同权重、同默认参数下更快」。

## 要点
- v26.9.1（9/3）：兼容模型支持 100 万 token 上下文窗口 + 更快的 Flash 解码；v26.9.2（9/9）：逐模型独立参数与更多 chat provider
- v26.9.3（9/16）：投机解码从限定模型扩到全部模型，并针对 64GB Mac 做 Flash 性能优化（配套 MLX Core App 侧栏加终端）
- 三种投机引擎（prompt lookup、外挂 drafter、模型自带 MTP head）默认开启；宣称贪心解码与参考逐字节一致、采样分布数学等价，每次发版跑字节等价测试
- 官方对比口径：同机同 4bit 权重、双方出厂默认下比 LM Studio 几何平均快 81%（prefill +36%），Qwen 3.6 27B 因加载 checkpoint 自带 MTP head 达 +145% decode
- 口径提醒：均为厂商自测（M4 Max / LM Studio 0.4.19 平台），需第三方复核；「MTP head 免外挂 draft」与 [[MLX生态2026]] 的 MTPLX 生态位同一条路线
