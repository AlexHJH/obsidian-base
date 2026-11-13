# CuTeDSL 的 sm100 epilogue 提前放行累加器：少一条 `tcgen05.wait::ld`，下一条 MMA 就能覆写 TMEM
- 来源: https://github.com/NVIDIA/cutlass/issues/3805
- 日期: 2026-10-31
- 标签: #NVIDIA #tcgen05 #TMEM #CuTeDSL #异步完成

## 摘要
`utils.gemm.sm100.epilogue` 在「非重叠累加器」分支的最后一个 subtile 上，`tcgen05.ld` 之后直接 `mbarrier.arrive` 释放 acc buffer，中间没有 `tcgen05.wait::ld`。这条 arrive 一唤醒 MMA warp 开始下一 tile，TMEM 里还没搬进寄存器的单元就被覆写。fuzz 在「每 block 多于两个输出 tile」时稳定复现（M256/N384、tile 128×192、单 cluster → 4 个 tile）。

## 要点
- 位置 `python/CuTeDSL/cutlass/utils/gemm/sm100.py:483-486`；重叠分支在 :478 调了 `cute.arch.fence_view_async_tmem_load()`，非重叠分支没有——同一文件两条路径的完成语义不一致
- 修法：在 :485 的 `with cute.arch.elect_one():` 之前插同一个 fence，PTX 里就会在最后一次 `tcgen05.ld` 与 arrive 之间出现 `tcgen05.wait::ld.sync.aligned`
- 违反的是 PTX ISA 9.7.18.6.4.4 例 1（Non-pipelined，different thread）：生产者必须在自己与「将要发下一条 `tcgen05.mma` 的线程」同步之前跑 `tcgen05.wait::ld`
- 复现版本 nvidia-cutlass-dsl 4.8.1 / CUTLASS `91843b7`；症状是结果错而非崩，正常单测静默，靠 fuzz 抓
- 因果要看全：CHANGELOG 新加的「opt-in TMEM accumulator-buffer planning + overlapping ping-pong」是为容量受限 kernel 提前放 buffer，fence 是随这条新路径补的；老的 last-subtile 放行沿用「arrive 即空」的假设就不成立了
- 判读：不是硬件坑，是「异步 ld 有完成 token，mbarrier.arrive 只保证 buffer 逻辑上空、不保证数据已进寄存器」这个模型细节没写全

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
