# SOSP'26｜EnerTune：只按利用率做 GPU 复用，反而更费电
- 来源: https://ut-sysml.ece.utexas.edu/publications/prints/sosp2026_sinha.pdf （SOSP'26, Prague, 2026-09-29~10-02；DOI 10.1145/3830418.3843913）
- 日期: 2026-09-29（会议）/ PDF 2026
- 标签: #SOSP2026 #GPU共享 #能效 #MIG #推理服务

## 摘要
生产集群 GPU 利用率常只有 10–50%，空间复用（MIG/MPS）是标准解法；UT Austin 这篇指出：只按利用率放置会把「energy-incompatible」模型凑到同一张卡（VGG19 + DenseNet161 同卡能耗比隔离跑高 1.3×，全局口径可达 1.6×）。EnerTune 用解析模型估单模型性能/功耗与同卡叠加功耗，喂给能耗感知 bin-packing，联合决定放置、GPU 配额、频率与 batch size。

## 要点
- 三条观测：① 以利用率为目标会增加能耗，放置须以能耗为一阶目标；② 配额与 batch size 要跨模型联合决定，否则多占 2–4 张卡、能耗 +1.4–1.7×；③ 降频只在 SLO 有余量时省电，最优频率随模型组合变（GPT/Whis/MNet 1.41→0.87 GHz 省 1.43×，SDXL/Whis/MNet 最优 0.96 GHz）
- 结果（真实 trace、A100 + MIG、5 个 SOTA 基线）：功率降 1.3–2.6×、能耗降 1.4–2.3× 且守住 p99 SLO
- 消融：把叠加功耗估计换成「各模型孤立功耗相加」的加性估计，平均少拿 1.3× 收益
- 副产物：解析建模把 profiling 时间压 1.32–17.3×（均值 7.3×），功耗估计误差 4–7%
- 可迁移之处：利用率是约束不是目标——同一逻辑适用于 L2 / 带宽预算分配
- 与本库已有 SOSP'26 笔记的分工：LLM-42 管确定性、Meld/MorphX 管内核形态、M2K 管接口符号化，这篇是唯一把「电」做成调度变量的
- 口径：数字取自 PDF 摘要与 §7，未复现；作者明说 A100 的 P0（被动散热）不能跨卡型迁移

关联：[[MLSys2026论文精选]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[推理引擎格局-vLLM-SGLang]]
