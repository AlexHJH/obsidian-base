# MLIR 新增 `match` 方言：把 PDL 的谓词调度独立出来
- 来源: https://discourse.llvm.org/t/rfc-match-a-new-dialect-in-between-pdl-and-pdl-interp/91847
- 日期: 2026-09-17
- 标签: #AI编译器 #MLIR #PDL #重写基础设施

## 摘要
RFC 提议在 `pdl` 与 `pdl_interp` 之间新增 `match` 方言，显式表达 `convert-pdl-to-pdl-interp` 里的谓词调度。原单体 pass 拆成 convert-pdl-to-match / match-combine-matchers / convert-match-to-pdl-interp 三步，IR 变得可内省、可在 match 层做变换，并让 PDL 能 AOT 编译成 C++ 重写模式。

## 要点
- 定位：`match` ≈「带结构化控制流的 pdl_interp」；提出者动机是在 Tamagoyaki 里用自定义 pdl→pdl_interp lowering（xDSL 实现）生成 e-matching 指令做等价饱和
- 关键收益：PDL→C++ AOT 重写模式（类似 DRR，但可把多个模式合成单个 matcher、原生支持多根模式）；长期对接 VeIR 在 Lean 里验证重写的路线（match 适合做成 Lean 单子嵌入 DSL）
- 信号：模式匹配 / 重写基础设施正在被重新分层，为「可验证的重写」铺路；作者放了 WASM playground 对比 pdl→match→C++ 的 lowering
- 关联 [[AI编译器2026格局]]
