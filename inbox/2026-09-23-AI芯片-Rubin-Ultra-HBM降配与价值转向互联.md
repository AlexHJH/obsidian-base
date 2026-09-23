# 英伟达 Rubin Ultra 内存降配：HBM 成本砍半，价值转向互联
- 来源: https://www.cls.cn/detail/2472752 （SemiAnalysis 报告拆解）
- 日期: 2026-09-03
- 标签: #AI芯片 #HBM #英伟达 #供应链

## 摘要
SemiAnalysis 拆解英伟达 Rubin Ultra 的降配动作：HBM 从 HBM4E 12-Hi（384GB）降到 HBM4 8-Hi（192GB），三星配合开发 8 层产品。降配后 HBM 成本降低 50% 以上，内存占产品总成本比例从约 40% 回落到 28%。

## 要点
- 动因是涨价而非设计失误：HBM/DRAM 大涨后内存已占总成本约 40%，砍容量是保毛利的选择
- 省下的钱没消失，而是流向纵向扩展网络：NVL576 NPO SKU 中 scale-up 网络占机架总支出比例从 4% 升到 12%（三倍），CPO 光互连成必选项
- 价值重心迁移：从「单卡 HBM 堆料」转向「大规模集群互联」；Rubin Ultra NVL576 原定 2027H2，Kyber 机架有延期传闻，PTFE 混压是潜在路线
- TrendForce：2026Q3 起英伟达对 Rubin Ultra 从 HBM4e 12hi 改为并行评估 HBM4e 8hi / HBM4 12hi / HBM4 8hi，部分 CSP 也在考虑下调自研 ASIC 的 HBM 容量
- 推理侧含义：192GB 仍够装主流 MoE 权重，但 KV cache 容量被压缩 → 对分层缓存与卸载方案的需求进一步上升
- 关联 [[HBF-NAND-AI推理内存2026]] [[推理引擎格局-vLLM-SGLang]]
