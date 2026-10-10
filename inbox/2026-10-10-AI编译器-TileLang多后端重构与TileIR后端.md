# TileLang：多后端语言方言重构 + CUDA Tile IR 执行后端（草案）

- 来源: https://github.com/tile-ai/tilelang/releases ; https://kubuds.io/ch/tilelang/weekly/2026/W38.html
- 日期: 2026-09 ~ 2026-10（W38 周报 + 最新 release）
- 标签: #AI编译器 #TileLang #TileIR #多后端

## 摘要
TileLang 最新 release 的主线是「多后端语言方言重构」：运行时激活的语言门面被替换为按后端静态 re-export，并新增两条硬件路径——Blackwell SM120 的 NVF4 block-scale MMA 与 Metal 4（M5）cooperative-tensor GEMM；同期还有 LLVM 后端与 backend-registry 重构。

## 要点
- 门面重构：`_activate_cuda_facade()` / `globals()` 一类运行时魔法被拆除，改为 `tilelang.cuda / metal / rocm` 静态重导出 + `.pyi`/`py.typed`；移除 `tilelang.common` 属破坏性变更
- 新后端：CUDA Tile IR 执行后端 PR（09-18，134 文件 / 约 3.6 万行）把 TileLang 程序下沉到 NVIDIA CUDA Tile IR、经 cuTile 运行时装载，与现行「生成 CUDA 源码交 nvcc」并行；改动覆盖 JIT/缓存/自动调优/文档/专用 CI，窗口末转草稿（#3247）
- 语义收紧：分块量化 GEMM 升为语言层算子 `T.gemm_blockscaled`，不具备该能力的后端由「静默按稠密 GEMM 执行、缩放因子被丢弃」改为**编译失败**
- 质量面：修复符号化循环布局单射性证明回归（静态+动态混合迭代空间、带填充尾部布局单射但非双射）；ROCm CI 接入四类可移植示例作回归资产
- 读法：与 Triton→CUDA Tile IR 后端同向——tile 语义在厂商自有 IR 上收敛，前端不再各自发明 lowering；对 GP+CC 分离 SoC 是现成范本
- 口径提示：未读全文，取自 release notes 与社区周报摘要
- 关联 [[AI编译器2026格局]]、[[推理引擎格局-vLLM-SGLang]]
