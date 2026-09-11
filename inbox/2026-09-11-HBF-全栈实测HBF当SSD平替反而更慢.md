# 全栈实测：把 HBF 当 SSD 平替做 KV 卸载反而更慢
- 来源: https://arxiv.org/abs/2608.11668
- 日期: 2026-08-25
- 标签: #HBF #KV缓存 #LLM推理 #论文

## 摘要
北大团队（Zhuoran Li、孙广宇、卓有为等）扩展 TokenSim，用 4 条两小时 Qwen-Bailian 生产 trace + 5 个稠密/MoE 模型 + H100/B200 剖面，验证「保留 Mooncake 式 KV 卸载栈、把底层 SSD 直接换成 HBF」的方案：升级后系统反而变慢，端到端延迟涨 2–5.5x，最大 SLO goodput 掉 1.1–2.7x。

## 要点
- 收益条件被写死了：更快的远端层只在「读 I/O 为瓶颈 + 读多于写 + 带宽可持续」时划算，而瞬态 KV 三条同时违反
- 写远多于读 → 3D-ICE 热模型显示栈在远低于峰值带宽处就撞热限；TLC 层比它替换掉的 SSD 池磨损更快
- HBF 自身读写延迟几乎不是瓶颈：延迟放大 3.75x，端到端延迟只变化 <1%；真实代价是占用封装内 near-tier 容量与带宽
- 结论：HBF 有价值但不能 drop-in，需要 reuse-aware 放置 + 写预算 + 热协调共同设计
- 关联 [[HBF-NAND-AI推理内存2026]] [[推理引擎格局-vLLM-SGLang]]
