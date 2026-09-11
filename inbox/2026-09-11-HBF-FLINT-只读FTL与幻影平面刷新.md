# FLINT：把 HBF 做成只读权重容量层（burst-buffer + 幻影平面刷新）
- 来源: https://arxiv.org/abs/2608.25062
- 日期: 2026-08-25
- 标签: #HBF #LLM推理 #内存系统 #论文

## 摘要
针对「HBF 作为与 HBM 并列的容量层存放 LLM 权重」路线，Oliveira、Onur Mutlu 等提出 FLINT：不追求通用性，而是把 HBF 当只读权重层专门优化，用三个机制消除现有方案的三类采纳障碍（粗粒度静态预取、把 NAND 刷新暴露到推理关键路径、闪存管理未按负载特化）。

## 要点
- burst-buffer 硬控器：动态合并并流水化 HBF 读，复用 NAND 片内 buffer 以维持高带宽，缓解 μs 级读延迟
- phantom-plane refresh：用低成本资源复制，把刷新操作移出前台读路径，不再进入 accelerator 可见的关键路径
- read-only FTL：放弃 SSD 级任意写支持，只保留「逻辑权重 burst → HBF 物理位置」的紧凑映射表
- 耐久：在 10^7 P/E 投影下可支撑连续推理约 8.0 年，足以让闪存留在高负载推理服务器里
- 关联 [[HBF-NAND-AI推理内存2026]]
