# Tensor Memory 是"要在 kernel 里抢的资源"：256KB/SM、按列分配、分配失败就自旋
- 来源: https://www.thesoftwarefrontier.com/p/how-blackwells-tensor-memory-actually
- 日期: 2026-08-03
- 标签: #NVIDIA #Blackwell #TMEM #tcgen05 #占用率

## 摘要
The Software Frontier 把 Blackwell 的 TMEM 当架构对象拆了一遍：256 KB/SM，128 lane × 512 列 32-bit 单元，地址是 (lane<<16)|column 的坐标而非线性地址；只有 tcgen05 家族能碰它，且由单个 elected thread 代表整个 CTA（或 `cta_group::2` 的两个 CTA）发起 `tcgen05.mma`。结论是它把一部分占用率计算从 launch 挪进了 kernel body。

## 要点
- 操作数规则被彻底反转：A 可在 SMEM 或 TMEM，B 必须在 SMEM，累加器必须在 TMEM，寄存器不出现在这句话里 → Hopper 那套 warp specialization 的动机被抽掉一半
- 访问按 warp 硬切：warp0 只够得着 lane 0–31，warp1 只够 32–63 → 抽一个 128 lane 累加器 tile 必须整个 warpgroup（CUTLASS `make_tmem_copy` 写死 4 warp）
- 分配是运行时原子竞争：`tcgen05.alloc` 按列（2 的幂、≥32），SASS 里落到 `UTCATOMSWS.FIND_AND_SET`，失败则 `NANOSLEEP 0x64` 无限重试；`ptxas` 无条件注入三个 guardrail trap（释放未分配列 / 跨分配释放 / 非法 phase），等于 use-after-free + double-free + 状态机断言
- 占用率变天：Blackwell SM 的寄存器 64K、warp 上限 64、block 上限 32、SMEM 228KB 全部与 Hopper 相同，一代翻倍的是 tensor 吞吐，新增容量只有 TMEM；而 512 列按 2 的幂切，最小合法块已把并发分配上限压到 16，而 Colfax 说 SM10x 上 `tcgen05.mma` 被锁死一 CTA/SM
- 带宽佐证：Delaware 组测得 B200 上 TMEM 读带宽约 16 TB/s（作者据此认为其推导的"约 4× 累加需求"合理）；并提醒该论文把 FP4 96.2% 的百分比算在比 datasheet 低约 11% 的峰值上
- 与我 11-05 记的 PTX 9.4/9.5 条目互补：那边是 fence/commit 粒度收窄，这边解释为什么要收窄——TMEM 上的状态是累加器 + 稀疏元数据 + block scale 三类共用 512 列
- 口径：该文为逆向 + 推导（SASS、ptxas 注入符号、CUTLASS 源码），非厂商披露；一处 SASS 助记符疑似手误，未在硬件上复现

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[2026-11-05-NVIDIA-PTX9.5同步收窄与sm107a稀疏压缩]]
