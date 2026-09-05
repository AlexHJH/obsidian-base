# Hot Chips 2026：OXMIQ 认为 HBF 无法替代 HBM，定位为「容量点」
- 来源: https://www.tomshardware.com/pc-components/ssds/hot-chips-2026-high-bandwidth-flash-promises-massive-bandwidth-and-capacity-but-its-usability-is-extremely-limited-new-memory-format-strikes-a-balance-between-hbm-and-nand-flash
- 日期: 2026-09-05
- 标签: #HBF #AI推理内存 #HotChips #HBM

## 摘要
GPU IP 公司 OXMIQ Labs 在 Hot Chips 2026 给出 HBF 冷思考：HBF 是「容量点」而非 HBM 廉价版，多数负载上无法替代 HBM，更适合超大但偏冷的数据集；推理软件栈未就绪是最大短板。

## 要点
- 建模 72-GPU 机架跑 Kimi-K2（1T 参数 FP4）：全 HBM 20.7TB / 1584TB/s；全 HBF 容量 14×（294.9TB）但带宽降至 922TB/s；混合 89.3TB / 279–1418TB/s
- vLLM 需专门改造（显存分配/数据放置/预取/闪存寿命监控），需 HBF 厂商 + 加速器厂商 + 推理框架三方协同；HBF 按 SSD 式大块对齐 DMA 访问
- 相关: [[HBF-NAND-AI推理内存2026]]
