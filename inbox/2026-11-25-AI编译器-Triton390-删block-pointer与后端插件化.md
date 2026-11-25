# Triton 3.9.0：删掉 block pointer，把「厂商后端」正式做成插件

- 来源: https://github.com/triton-lang/triton/releases/tag/v3.9.0
- 日期: 2026-10-27
- 标签: #Triton #AI编译器 #MLIR #Gluon #破坏性变更

## 摘要
Triton 3.9.0（2026-10-27）是继 3.8.0 之后的一版结构性发布：语言层删掉了 block pointer，编译层把 barrier 插入独立成 pass，扩展层则把插件注册、外部 layout、独立 LLVM pin 全部制度化。对本库的 Triton→RISC-V 路线，后两项比性能数字更值得读。

## 要点
- 最大破坏：`tl.make_block_ptr` 直接抛 `NotImplementedError`、`tl.advance` 移除、`tl.load/store` 不再收 `boundary_check`/`padding_option` → 改用 tensor descriptor（`tl.make_tensor_descriptor` 或 host 侧 `TensorDescriptor`）；存量教程与老内核会成批失效
- 数值语义收紧：3.10 将默认关闭隐式 FP fusion；3.9 起每进程告警一次，`TRITON_DEFAULT_FP_FUSION`/`TRITON_FORCE_DISABLE_FP_FUSION` 控制。另有 `/`、`%` 在窄浮点下返回 fp32，标量不再拓宽 `tl.minimum/maximum/clamp` 结果
- 第三方后端的第一等公民化：out-of-tree MMA/shared encoding 走 `MmaEncodingTrait`/`SharedEncodingTrait`；插件经 `extend_with`/`extend_dialects_with` 运行时注册（`TRITON_PLUGIN_PATHS` 退役）；`TRITON_EXT_ENABLED=1` 出带 C++ headers + TableGen 的 wheel
- 模板意义（推断，非原文）：AMD 后端已用独立 LLVM pin（`triton-lang/llvm-project@6bc4aaf6`）+ `TRITON_AMD_CODEGEN_PATH`，即「非 NVIDIA 后端可以不吃上游 pin」——这是第三方 ISA 后端可复用的组织方式
- Gluon：可离线编译 `.glir`（无 GPU 交叉编译）、`gl.inline_asm` 每线程内联汇编（可吃 SMEM/TMEM 描述符）、`cache_policy` 控 L1/L2 + fractional eviction + L2 prefetch size、Blackwell 256-bit 全局访存
- 持久化：SM100+ 上 `clc=True` 把「一 program 一 tile」的 kernel 变成 CLC 持久循环，且兼容 `warp_specialize=True`
- 并发正确性：barrier 插入重写为独立 pass（补缺、删冗余）；`acquire`/`release`/`acq_rel` 原子插 block 级 barrier，RMW 与 CAS 默认 `acq_rel`，可用 `sem="relaxed"` 规避
- 性能数字（原文口径）：SM103 的 FP4×FP4 kernel 快 6–15%；MX/FP4 layout 转换快 4–40x、峰值内存减半；NVIDIA batched MMAv2 dot 最多 40x；嵌套 8 层循环编译快约 10x
- 工具链：Python 3.10 移除（要求 3.11–3.15）、单 `cp312-abi3` wheel、绑定从 pybind11 迁到 nanobind、C++20；sm_90 起用 CUDA 13 ptxas（路径改 `TRITON_PTXAS_BLACKWELL_PATH`）

关联 [[AI编译器2026格局]]、[[RISC-V矩阵扩展两年进展-2024-2026]]
