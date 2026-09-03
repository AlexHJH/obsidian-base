# ASPLOS 2026：PIM 存内计算 + KV Cache 卸载成 LLM 推理新焦点
- 来源: https://www.asplos-conference.org/asplos2026/program/index.html
- 日期: 2026-09-03
- 标签: #MLSys #ASPLOS #PIM #KV-Cache #大模型推理

## 摘要
ASPLOS 2026 录用论文中，PIM（存内计算）与 KV Cache 卸载成为 LLM 推理主线：多篇论文用可重构 PIM 把 KV 搬移与计算协同，直击主机内存带宽瓶颈，与 HBF/CXL 内存外移趋势同频。

## 要点
- REPA（SJTU）：可重构 PIM 联合加速 KV Cache 卸载与处理，co-design 数据搬移与计算
- STARC（RPI/UMass/IBM）：token 选择性访问 + 重映射/聚类，正交于量化与淘汰式压缩
- ZipServ（港科广/哈工深/港科）：硬件感知无损压缩降内存占用且不损精度
- 相关: [[MLSys2026论文精选]] [[AI芯片推理硬件2026全景]]
