# PTX 9.4 把 2:4 稀疏的「选哪两个」做成指令，但只在 sm_107a 可用
- 来源: https://docs.nvidia.com/cuda/parallel-thread-execution/ （9.7.10.31 spcompress / 9.7.10.32 spdecompress；PTX ISA 9.5 ↔ CUDA 13.5 / r620）
- 日期: 2026-11-16（对照 PTX ISA 9.5 在线手册）
- 标签: #NVIDIA #PTX #稀疏 #sm107a #Rubin

## 摘要
PTX 9.4 新增 `spcompress` / `spdecompress` 一对数据搬运指令：在稠密向量与 2:4 结构化稀疏向量之间互转，metadata 索引就是选中的元素位置。两者 Target ISA 只写 `sm_107a` 一行——这是 Rubin 专属指令，Blackwell（sm_100/103）上没有。

## 要点
- `spcompress.elemsize.idxsize.sp::2:4.num mdata, cdata, data, spdesc`：压缩因子只有 `.sp::2:4` 一种；`.elemsize` = b8/b16，`.idxsize` = b2/b4，`.num` = x1…x64（x64 时 data 是 128 个 .b32）
- 选择规则放在 32 位 `spdesc` 里：bit0-1 = MAX/MAXABS/MIN/MINABS，bit2-4 = 元素类型（.f16/.u8、.bf16/.s8、.e5m2、.e4m3、.e3m2、.e2m3）；类型位与 `.elemsize` 冲突是运行期报错
- 文档明写的不确定面：一组 4 个里满足条件的多于 2 个、或并列值时，保留哪两个索引是 implementation-specific；NaN 一定被选中；`-0.0 < +0.0`
- 反方向 `spdecompress` 因子多得多（1:2 / 1:4 / 1:8 / 1:16 / 2:4 / 2:8 / 2:16 / 4:8 / 4:16），空缺位置补零；约束：单次迭代 `num_src*elemsize ≤ 32` 位、`.b2` 索引只能喂 `num_target ≤ 4`、data 最多 128 个 .b32
- 9.5 顺带补了 TMA 描述符的分配类别：`tensormap.replace` 新增 `.alloc_class`（.alloc_small/.alloc_large/.alloc_same），配 `tensormap.query.alloc_class_large` 查询；`global_address` 字段不带 alloc_class 时默认 Small
- 架构清单里 sm_107/sm_107f/sm_107a 与 sm_110/110f/110a、sm_121 并列出现 → 家族化 target 已经成型，`-arch` 判据不能再只盯 sm_100a
- 判读：稀疏化下沉成「格式转换」而不是「另一个 MMA」，说明数据面在动而非张量核在动；上一条 Rubin 笔记（smem +100KiB 换第五级流水）讲的是容量，这条讲的是稀疏数据的产生端

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[Nvidia-GPU微架构演进]]
