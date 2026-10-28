# CUTLASS 4.8 首发 Rubin SM107：TMEM 576 列、MMA_K 翻倍
- 来源: https://docs.nvidia.com/cutlass/latest/CHANGELOG.html
- 日期: 2026-10-07
- 标签: #NVIDIA #Rubin #CUTLASS #tcgen05 #PTX

## 摘要
CUTLASS 4.8 加入 Rubin（SM107）初版支持：CuTe C++ 侧提供 Rubin Tensor Core MMA atom 与 4 个 block-scaled 示例，硬件参数比 Blackwell 涨一档——TMEM 每 SM 576 列（原 512）、共享内存 327KiB、FP8 MMA_K=64 / FP4 MMA_K=128。CUDA 13.4（10-07 直播）同步给出 sm_107 developer preview 与 PTX ISA 9.4。

## 要点
- 目标名 `sm_107a` / `sm_107f`；SM100 的 GEMM/conv 可直接跑，TMEM 分配器改为 Rubin 独占路径
- TMEM 512→576 列、smem→327KiB：直接放宽 accumulator 驻留与深流水层数，是「同指令族、更大 MPSM 预算」而非新指令集
- 吞吐手段是 MMA_K 变宽（FP8 64 / FP4 128）+ B collector reuse，不靠新增 warp 级指令
- PTX ISA 9.4（CUDA 13.4 U1，13.4.1 于 09-09 发）新增：`cvt` 的 `.pzo`、`tcgen05.commit` 的 `.sync_restrict::shared::read::mma::a`
- 口径提示：docs.nvidia.com 本次正文抽取被拦，以上均出自搜索快照中的原始文本片段，PTX 9.4 完整变更清单未逐条核对
- 关联 [[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[AI芯片推理硬件2026全景]]
