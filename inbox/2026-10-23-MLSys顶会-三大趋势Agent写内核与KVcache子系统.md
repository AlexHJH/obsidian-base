# MLSys 2026 三大趋势：Agent 写内核、KV cache 变成子系统、异构硬件成为前提
- 来源: https://www.modular.com/blog/three-trends-from-mlsys-2026
- 日期: 2026-05-29
- 标签: #MLSys #LLM推理 #KVcache #Agent

## 摘要
Modular 团队对 MLSys 2026 的复盘：LLM serving 的 session 数量翻倍到 6 个，三条主线贯穿全场——agent 开始写内核与系统代码、KV cache 从引擎内部细节升级为独立子系统、异构硬件成为 serving 的前提假设。三条趋势的共同后果是"验收与验证"的权重上升。

## 要点
- Saroufim 主题演讲：agent 写的内核能打进 kernel 竞赛前列，但会作弊刷分、不为泛化付出代价 → 需要"零信任"式足够严的 benchmark
- 周礼栋 keynote（Nanvix Rust 微内核）：AI 生成 spec+证明逐模块验证，150 任务证明生成通过率 2%（GPT-4o）→ 91.3%（微调 LLaMA-3.1 8B + 自调试）；失败时模型会 `external_body` 绕过验证器、伪造 postcondition、把证明负担推给调用方
- LMCache 实测：五周 telemetry 中 KV cache 主体已放不进 GPU，每 token 复用次数涨 19%+；支持 8 种存储后端 × 4 种处理器 × vLLM/SGLang
- Kitty（2-bit KV，动态 channel 精度）8 倍 batch；SGLang HiSparse 用分层内存（GPU 热区 + host 冷区）在 GLM-5.1-FP8 长上下文上最高 5x 吞吐
- 已经出现专为 cache 做的芯片（Netpreme 的 Memory Processing Unit），子系统开始拉自己的硬件厂商 → 不再是实现细节
- 硬件侧：SuperInfer 指出 GH200 的 NVLink-C2C 900 GB/s 被当成 PCIe 用，利用率 <5%，瓶颈在软件栈
- 与 [[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]] 衔接；对我们的映射：LongLoop 的"防虚假完成"与这里的 zero-trust 验证是同一问题
