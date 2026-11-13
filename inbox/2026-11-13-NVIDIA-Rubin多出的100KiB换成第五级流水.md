# Rubin 多出来的 100 KiB smem 不是空间，是第五级流水；NVFP4 实测 22.2 PFLOPS 只到峰值 64%
- 来源: https://www.together.ai/blog/to-infinity-and-beyond-thunderkittens-now-on-nvidia-vera-rubin-nvl72
- 日期: 2026-09-10
- 标签: #NVIDIA #Rubin #SM107 #NVFP4 #pipeline #实测

## 摘要
Together 的 ThunderKittens 拿到 NVL72 早期访问后，把 16k NVFP4 GEMM 推到 22.4 PFLOPS、FP8 约 12 PFLOPS。做法是拿原有 Blackwell kernel 逐条套 Rubin 的新条件：MMA K 从 32 字节加宽到 64、TMEM 576 列、smem 328 KiB、B collector reuse。作者附的 stage 扫描表才是重点——收益来自 smem 换来的流水深度，不是新指令本身。

## 要点
- NVFP4 16k：3 stage/202 KiB → 17,054 TFLOPS；4 stage/258 KiB → 20,595；5 stage/314 KiB → 22,239。第 5 级要 314 KiB，已越过 Blackwell 的 228 KiB 上限，Rubin 那 +100 KiB 的唯一作用就是买到这一级
- 边际收益在收窄：3→4 级 +20.7%，4→5 级 +8.0%，说明瓶颈开始从流水深度转向别处
- FP8 反过来：4 stage 10,895 → 5 stage 11,995 → 6 stage 11,288，到 6 级反噬；堆 stage 不是线性收益，得换 tiling 或 collector 复用
- 对官方峰值（NVFP4 35 PFLOPS/GPU、FP8 17.5）分别是约 64% 与 68.5%；「competitive with cuBLAS/CuTe DSL」是作者自述口径，非第三方复现
- 表格最大 22,239 TFLOPS，推文说 22.4 PFLOPS——引用时别混用两个数
- B collector reuse 目前只支持 A 在 M 方向复用、B 不离开 collector，且不支持 B 转置；这是 Rubin 相对 Blackwell 真正新增的复用面
- 结构判读：Rubin 是 delta 不是 reset——warp specialization / TMA / 2-CTA cluster / persistent scheduler 全不变，要学的只有三件：更大片上存储、K 加宽、collector 复用

关联：[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[Nvidia-GPU微架构演进]]
