# PTX 9.4 给 TMA/mbarrier 加了 32 位多播和异步驱逐优先级
- 来源: https://docs.nvidia.com/cuda/parallel-thread-execution/index.html
- 日期: 2026-11-19
- 标签: #NVIDIA #PTX #TMA #mbarrier #tcgen05

## 摘要
当前 PTX 文档版本为 9.5。真正动到异步拷贝/tensor core 面的是 9.4：新增 sm_107/107f/107a 目标，把 cluster 多播从 16 位掩码扩到 32 位，并为 bulk 预取配上可异步生效的 L2 驱逐优先级。9.5 的新增项集中在 fabric（多卡/多 die 一致性）与 tensormap，没有新的矩阵指令。

## 要点
- `.multicast::cluster::32b`：`cp.async.bulk(.tensor)`、`mbarrier.expect_tx/arrive`、`tcgen05.commit` 都可带 32 位 CTA mask，但仅 sm_107f 及同族更高型号支持——cluster 超过 16 CTA 才用得上
- `applypriority.async.bulk(.tensor)`：把「设置缓存驱逐优先级」做成异步指令（目前只有 `.L2::evict_normal` + `.bulk_group` 完成机制），可与 `cp.async.bulk.prefetch` 的 `.level::eviction_priority` 串联，在预取时就决定数据在 L2 的落点
- `mbarrier::report::validity::per_16bytes::8000`（`.report_mechanism`，限 sm_107f+）：TMA 完成后按每 16B 用 0x8000 模式做有效性校验，等于给 bulk 拷贝加了逐段的完成证据
- `tcgen05.commit` 新增 `.sync_restrict::shared::read::mma::a`，约束共享内存读取与 MMA 之间的同步域
- 9.5 一侧：`tensormap.replace` 的 `.alloc_class` 区分 Tensor Allocation Size Small/Large（未指定即 Small），另有 `fabric.submit.commit_group`、`fabric.wait.sync_restrict::read` 取代旧的 `::reads`

关联 [[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[Nvidia-GPU微架构演进]]
