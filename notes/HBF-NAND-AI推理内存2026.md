# HBF (High Bandwidth Flash) — AI 推理内存新层级

> 种子文档（2026-08-11 创建）。后续由知识库采集 cron 每天更新进展。

## 一句话

把 NAND 用 HBM 的封装工艺（TSV 堆叠）做成新的内存层级：**容量 8–16x HBM、带宽接近 HBM、延迟 ns 级（HBM）→ μs 级（NAND）、写耐久低、功耗偏高**。定位是"推理专用内存层"，不是 HBM 替代品。

## 关键参数对比

| | HBM4 | HBF | Kioxia 原型 |
|---|---|---|---|
| 容量 | 48–64GB | ~8–16x HBM | 5TB |
| 带宽 | 1.6–2 TB/s | 接近 HBM | 64 GB/s |
| 延迟 | ns | μs | μs |
| 功耗 | — | ~4x HBM/bit | <40W |
| 写耐久 | ∞ | 低（NAND 本质） | — |

## 玩家格局（截至 FMS 2026）

- **SanDisk + SK Hynix 联盟**：2025 MoU → 2026.2 OCP 标准化项目（Google、Tenstorrent 已加入）。样品 **2026 H2**，AI 推理设备样品 **2027 初**，~2028–2030 放量。Micron 缺席联盟。
- **Samsung 单干**：FMS 2026 主推 zHBM（HBM 直接堆 GPU 上，宣称 ~8x HBM5 性能、10x 密度）+ V10 BV-NAND（400+ 层）。
- **Kioxia**：2025.8 原型——5TB / 64GB/s / <40W，PCIe 6.0 + 128Gbps PAM4 菊花链控制器，模块化路线。

## LLM 推理应用

1. **模型权重**：冻结、只读、确定性访问 → 完美匹配 NAND。每节点权重容量 ×10，直接减少所需 GPU 数量。
2. **冷 KV cache**：Llama 4 的 10M token 上下文需 5.4TB KV cache。HBF 承接冷 KV，HBM 只做热工作区。
3. **H³ 论文（SK Hynix+KAIST, IEEE）**：HBM+HBF 混合推理，性能/瓦 +2.69x；10M token KV 场景并发查询 18.8x；32 GPU 负载 → 2 GPU。
4. **软肋**：KV cache 每 token 新写，NAND 耐久扛不住 → 需写合并/追加写策略（H³ 已处理）。

## 对系统架构的影响（异构 SoC 视角）

- 内存层级 HBM/DRAM/SSD 三级 → **HBM + HBF + SSD 四级**，HBF 是贴加速器的高带宽持久层
- 权重确定性访问 → 硬件预取/调度可提前规划，SoC 调度器新战场
- 需新内存控制器接口（NAND 协议 + 高带宽 TSV）+ 写合并、磨损均衡软件栈
- CXL 内存池同生态位竞争，但带宽差一个量级

## 来源

- SanDisk 官方博客（2025）：Scaling Beyond the Wall
- HPCwire (2026-08-04)：What is High Bandwidth Flash (HBF)
- IEEE Spectrum：High Bandwidth Flash Unlocks Massive Model Storage
- Chipstrat：High Bandwidth Flash: The Full Report
- Kioxia 新闻稿（2025-08-20）：5TB 64GB/s 原型
- SemiWiki / Blocks & Files：SK Hynix HBM+HBF hybrid（H³ 论文）
- Samsung FMS 2026 新闻稿：zHBM / V10 BV-NAND
