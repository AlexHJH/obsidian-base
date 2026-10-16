# Hot Chips 2026 复盘：HBF 是「容量层」不是 HBM 替代；HBFSim 在真 GPU 上仿真 HBF
- 来源: https://www.tomshardware.com/pc-components/ssds/hot-chips-2026-high-bandwidth-flash-promises-massive-bandwidth-and-capacity-but-its-usability-is-extremely-limited-new-memory-format-strikes-a-balance-between-hbm-and-nand-flash ; https://chipsandcheese.com/p/hot-chips-2026-applying-high-bandwidth ; https://www.servethehome.com/oxmiq-labs-hbf-in-ai-compute-at-hot-chips-2026 ; https://arxiv.org/html/2609.09800v3
- 日期: 2026-08（Hot Chips 2026）/ 2026-09-30（HBFSim 提交）
- 标签: #HBF #内存墙 #KV缓存 #MoE #仿真

## 摘要
OXMIQ Labs（Raja Koduri 创办）在 Hot Chips 2026 明确指出 HBF 在绝大多数负载下不能替代 HBM——它是内存层级上的一块「容量点」，不是廉价版 HBM。真正适配的是「写一次、读多次」的数据：MoE 专家权重、可共享的系统提示 KV。同期论文 HBFSim 给出不等硅片的验证路径：只仿真 HBF 行为，GPU 与模型保持真实执行。

## 要点
- 量化对比（72 GPU 跑 1T 参数 Kimi-K2 @FP4，成本/功耗对等）：纯 HBM 20.7TB 容量 + 1584TB/s；纯 HBF 容量 ×14 到 294.9TB 但带宽掉到 922TB/s（约 0.6×）；混合配置 89.3TB、279–1418TB/s（随负载）
- MoE 是最佳落点：Kimi-K3 权重 1.56TB，其中 1.45TB（93%）是专家权重，按 token 稀疏激活 → 放 HBF 几乎写一次；顺带减少 expert-parallel 跨设备通信
- 长上下文 KV 缓存有条件可行：只适合稀疏注意力（每步只读 top-k），且 top-k 行是散读、HBF 偏好顺序读，需靠 DMA 聚合绕开
- 接口细节：读 64KB / 写 1MB 粒度走 DMA，带宽档 0.384–3.072TB/s、UCIe 8→32GT/s、16 层堆叠 512GiB；写入寿命需监控
- 软件是最大阻塞：vLLM 需专门的 HBF 插件（数据布局、预取、寿命管理），AMD/NVIDIA 需硬件+驱动支持跨层迁移，缺任一方都不成立
- 进度右移：闪迪 8/13 投资者日首次展示已流片（tape-out）的 HBF die 图，但「首批 HBF 推理产品样品」由 2026H2 改列为 2027
- HBFSim 思路：只把注册过的内存区间改写到在线 HBF 模型，未注册访问走原生路径；Qwen3-30B-A3B 插桩运行输出 token 完全一致（42 项检查 + 128 校验和），110GiB 逻辑区间经 2GiB 页缓存映射
- 判据：仍是「仿真 + 研报 + demo」，没有硅片实测；下一真实检查点是流片后的 fab 数据与客户样品
- 关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]
