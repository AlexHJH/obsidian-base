# Google Coral NPU 已按 Zvt v0.4 做出硅，官方 v1.0 才排到"2026 年底"
- 来源: https://developers.google.com/coral/guides/hardware/vme-matrix-engine ；https://lists.riscv.org/g/tech-vme/rss
- 日期: 2026-10-19（Coral 文档更新）；列表最新帖 2026-10-29
- 标签: #RISC-V #VME #Zvt #边缘NPU #规范进程

## 摘要
Google 的 Coral NPU 文档明确写：其矩阵引擎实现 Zvt v0.4，用 128 位 RVV（Zve32x）寄存器做源操作数、16 个 tile 累加器，走外积 MAC；同一页同时声明"该规范正式 v1.0 版排在 2026 年底发布"。也就是说硅与编译器已经跑在草案上，标准还在 BoD 前。

## 要点
- 微架构取舍：矩阵引擎是与向量引擎解耦的协处理器，走命令队列 + 多级流水；`mset*` 先定 M×K×N 分块，再由 µop 驱动 PE 阵列（含 `REMVPIPEBUBB` 气泡处理、累加器饱和/FP 异常跟踪）
- 与 IME 的分野被写进官方话术：IME 只能用现有 RVV 寄存器堆、受容量限制；VME 加独立累加器寄存器堆，但保持"无新增 2D load/store、按行/列主序吃普通内存"
- 列表侧的最新状态（非规范状态）：10-29 ARC 对 IME/VME 编码撞车给出裁断——建议 **IME 侧改用 27:26=10**，理由是更接近 `vlse/vsse` 的 strided 访存编码，这收敛了 11-05 记录的冲突
- 10-19 Greg Favor 对"规范 vs Spike 的 punning 偏移公式不一致"只回一句"Spike is out of date" → 口径定为规范正确、参考实现待改，但 Spike 补丁未见
- IME 侧对照：`riscv/integrated-matrix-extension` 最后一次提交停在 08-04（Overview 改写），9 月以来无动作 → 两条路线的时间差正在拉大
- 待核：11-04 TSC 表决结果未能从公开渠道确认（Ratification Plan 页为 JS 渲染，tech-vme 列表 10-29 后无新帖）；"v1.0 年底"是厂商文档口径，不是 RVIA 公告

关联：[[RISC-V矩阵扩展两年进展-2024-2026]]、[[2026-11-05-RISC-V-IME与VME编码撞车]]、[[AI编译器2026格局]]
