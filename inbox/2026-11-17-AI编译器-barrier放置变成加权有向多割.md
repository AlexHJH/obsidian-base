# Triton 把 barrier/TMEM wait 放置建模成「加权有向多割」，并给出最优性下界
- 来源: https://github.com/triton-lang/triton/pull/12752
- 日期: 2026-11-13
- 标签: #Triton #AI编译器 #mbarrier #TMEM #图算法

## 摘要
Membar 从「按 pattern 补屏障」改成「在函数/WS region 的 CFG 上求全局最优放置」：顶点是带 SMEM/TMEM 副作用的 op，边是执行序，放置等价于加权有向多割，权重取 4^循环深度。有向多割 NP-hard，作者用原始-对偶启发式同时给出可行解和最优值下界，28 个 kernel 上界全部对上。

## 要点
- 冲突检测：在图上跑 flow pass 找「可能产生 race」的节点对，而不是逐个 pattern 匹配；feasible 集里显式包含「跨 warp TMEM 交接要 wait 后再 barrier」这种复合约束
- 代价模型只用循环深度：`cost = 4^loopDepth`，等价于假设每层循环执行 4 次；不需要知道实际 trip count——这让问题留在静态求解域内
- 输出不只是一个放置，还有 `L ≤ OPT ≤ C(S)` 的双侧界；界相等即证明该放置在此代价模型下最优（作者称 28/28 相等）
- 与 [[#Triton 3.9.0：clc 把普通 kernel 变持久循环，Gluon 下探到 inline asm 与 cache policy（2026-11-11 归档）]] 那条「barrier 插入独立成 pass」是同一件事的下一步：独立 pass 只是收口，这里是给它一个可验证的目标函数
- 对自研后端的含义：async 管线的正确性约束是「序」的关系，天然是图上的割问题；把代价写成循环深度的幂，比手写一堆 `if is_pipelined` 更可迁移
- 口径：PR 仍为 open（依赖 #12745），性能收益本文未给；28 个 kernel 的评估集构成未披露
- 关联 [[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[AI编译器2026格局]]
