# 国内路线：九天睿芯把 HBF 与 SRAM 存算一体堆叠成「单卡跑万亿 MoE」
- 来源: https://www.reexen.com/hbf/index.html ; https://zhuanlan.zhihu.com/p/2074107493312157012 ; https://m.zhidx.com/p/582110.html
- 日期: 2026-10（页面无发布日，按采集日）
- 标签: #HBF #存算一体 #国产芯片 #大模型推理

## 摘要
九天睿芯（深圳）走的是 HBF × SRAM 存算一体（SRAM-CIM）堆叠路线：用 TSV + Hybrid Bonding 把 HBF 与 SRAM-CIM 垂直集成，HBF 承载模型权重、KV Cache 与 MoE 专家参数，CIM 在 SRAM 阵列内部完成高频乘加，目标是单卡跑万亿参数 MoE 推理。

## 要点
- 分工不是替代而是分层：HBF = 近计算大容量层（权重/KV/MoE 专家），SRAM-CIM = 热数据与高频计算层
- 方法论叫 MPM（Memory–Processor–Model）共生设计，与 HBF 阵营「数据放置变成软件问题」的判断同源
- 产能口径：HBF 承载大容量数据、减少权重搬运功耗是核心卖点，宣称带宽达数 TB/s、可替代 HBM 做显存
- 证据边界：芯片仍在研（去年起与存储伙伴共同研发），未见第三方实测，属厂商路线声明
- 关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]
