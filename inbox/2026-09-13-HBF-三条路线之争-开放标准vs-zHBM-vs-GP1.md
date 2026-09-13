# HBF 三条路线之争：开放标准 vs 三星 zHBM vs 铠侠 GP1
- 来源: https://nand-research.com/high-bandwidth-flash-bridging-the-gap-between-expensive-hbm-flash-memory
- 日期: 2026-09-13
- 标签: #HBF #内存分层 #AI推理 #三星 #铠侠 #OCP

## 摘要
NAND Research（8/23）拆解 HBF 首版 OCP 规范与竞争路线：HBM 约 20–30 美元/GB 已把 TB 级封装内存推到经济不可行，HBF 想在同等成本下换 8–16× 容量。但标准目前缺少商用 GPU 厂商支持，是落地最大变量。

## 要点
- 规范：8/16 层 NAND 堆叠、单封装最高 512GB（首代 die 256Gb）、三级带宽 0.4–3.0 TB/s，走 UCIe 而非私有 interposer
- 路线图：Gen2 读带宽 >2 TB/s / 1TB 堆叠，Gen3 3.2 TB/s / 1.5TB
- 联盟缺口：创始成员仅 SK 海力士、闪迪、Google、Tenstorrent——英伟达、AMD、Intel、博通、Marvell、美光、三星、高通、西数均未加入
- 竞品：三星 zHBM 把 HBM 直接堆在加速器上（无公开规范）；铠侠走 PCIe 6.0 SSD 路线（GP1，1000 万 IOPS），2026 年底送评估样机，早于 HBF 的 2027 商用但带宽更低
- 落地前提：HBF 必须在 tape-out 阶段设计进加速器封装（无法后装），推理栈需新增内存分层调度；H³ 的 2.69× 吞吐/瓦尚无独立验证
- 关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]
