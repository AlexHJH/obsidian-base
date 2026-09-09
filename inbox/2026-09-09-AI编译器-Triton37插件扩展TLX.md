# PyTorch-Triton 3.7 引入 Plugin Extensions：TLX 免编译进上游 Triton
- 来源: https://pytorch.org/blog/triton-plugin-extensions-enabling-tlx-and-custom-compiler-passes-out-of-the-box
- 日期: 2026-09-09
- 标签: #AI编译器 #Triton #MLIR #TLX #Meta

## 摘要
PyTorch-Triton 3.7 发布「Triton Plugin Extensions」插件系统：以共享库(.so)形式在运行时动态加载自定义编译 pass、Dialect(含算子)与 DSL 扩展，无需 fork 或重编译上游 Triton。首个大消费者是 Meta 的 TLX（Triton Language Extensions），现已开箱即用。

## 要点
- 插件经 TRITON_PLUGIN_PATHS 环境变量发现加载，覆盖整条编译流水线，可覆写编译器 pipeline
- TLX 由此从 Meta 实验性 fork 转为独立 Python 包 `utlx`，跑在未修改的上游 Triton 上；3.7 起默认启用
- TLX 提供持久化 GEMM kernel 与细粒度硬件控制（显存/异步计算原语），性能追平或超越手写实现
- 跨硬件验证：NVIDIA H100 与 AMD MI350
- 关联: [[AI编译器2026格局]]
