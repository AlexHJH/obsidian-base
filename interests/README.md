# 我的关注

> 泡泡啦定期扫描这些主题，整理到这个知识库。
> 添加新主题：直接加一行 `- 主题名`
> 暂停某个主题：在行首加 `#` 注释掉

## 当前关注

- AI Agent 框架 & 工具
- 量化交易策略 (A股)
- MLX / Apple Silicon 推理
- AI 芯片 & 半导体
- MLSys / OSDI 等系统顶会
- AI 编译器 (TVM, Triton, MLIR)
- 大模型推理 & 部署
- HBF (High Bandwidth Flash) & AI 推理内存
- RISC-V Matrix 扩展上游社区进展 ([IME](https://github.com/riscv/integrated-matrix-extension) / [VME](https://github.com/riscv/riscv-vme) / [AME](https://github.com/riscv/riscv-attached-matrix-extension))：关注官方规范、TG 会议纪要/邮件列表、Issues/PRs，以及 LLVM/GCC/binutils、Linux/QEMU 上游支持；区分提案/草案、已批准标准、实现合入状态与厂商扩展
- NVIDIA Tensor Core / TMA 与 AI 指令体系：关注 [PTX ISA](https://docs.nvidia.com/cuda/parallel-thread-execution/) 中的 mma/wgmma/tcgen05、cp.async/TMA、mbarrier/async proxy、Tensor Memory、低精度与块缩放，以及 CUDA、CUTLASS/CuTe、Triton 上游映射；按架构与版本区分硬件能力、指令语义、编译器/库支持和实测性能
- 大模型推理性能与成本基线：200B–2T、DDR/GDDR/HBM、P95 TPOT <100 ms、并发与 MTP/EP、达标 token 成本；数据源：MLPerf Datacenter、ServeGen、官方模型仓、Dynamo、llm-d、API 官方价目。
- K1/K3 上游变更与回归：RVV/IME 派发、MTP 回滚、Triton/TLE tile lowering；数据源：llama.cpp 上游与 SpacemiT fork、FlagTree、Triton releases、LLVM/GCC。
- 低精度量化格式与硬件实现：MXFP4、实验性 MXINT4、NVFP4 的 FP16 转换、PTQ/QAT、硬件与真实派发；数据源：OCP MX、CUTLASS、MLX、AMD Quark。
- 芯片微架构、内存和互联实测：优先 [Chips and Cheese](https://chipsandcheese.com/archive)、ServeTheHome、OpenBenchmarking/Phoronix、TechPowerUp GPU Database、Hot Chips、UCIe Consortium、OCP HBF。详见 [[芯片架构与实测数据源]]。
- AI 芯片地图（[[AI 芯片地图]]）：扫国内外已经发布和官方披露的产品，含 Mac、AI PC、边缘、GPU/NPU/ASIC；AI 算力 ≥80 TOPS 或标明精度的矩阵算力 ≥80 TFLOPS，且外部或统一内存带宽 ≥128 GB/s；记录 SKU、口径、精度、稀疏和一手来源，缺项列待核。

> 新增主题优先收改变成本假设、影响代码或值得复跑的证据。注明规格、供应商目标、独立实测和自测的区别；不自动收录未获授权的内部日志、客户 trace 或报价。
