# Triton 3.8 已发布（AArch64 轮子），插件扩展系统让自定义 pass/dialect 免 fork

- 来源: https://github.com/triton-lang/triton/blob/main/RELEASE.md , https://pytorch.org/blog/triton-plugin-extensions-enabling-tlx-and-custom-compiler-passes-out-of-the-box
- 日期: 2026-10-02（3.8.0 发布 2026-08-28；插件系统博文 2026-07-15）
- 标签: #AI编译器 #Triton #MLIR #Gluon #TLX

## 摘要
Triton 3.8.0 于 8/28 发布（release 分支 6 月切出），wheel 覆盖 x86-64 与 AArch64、Python 3.10–3.14；下一站 3.9 目标 10/21（随 PyTorch 2.15）。更值得记的是 PyTorch-Triton 3.7 引入的插件扩展系统：自定义 MLIR pass/dialect 与 DSL 扩展以 .so 形式运行时加载，不必再维护私有 fork。

## 要点
- 加载方式：`TRITON_PLUGIN_PATHS` 指向 .so，无需重编译 Triton；hook 埋在 backend `compiler.py`，覆盖 TTIR → TTGIR → LLVM IR → PTX/AMDGCN 全程，可插入/禁用/替换单个 pass，甚至整段管线覆盖（NVIDIA 与 AMD 后端均支持）
- 三级扩展：单 pass 变换 / 自编译 dialect + conversion（把标准 Triton IR 改写成自有 op）/ 顶层 DSL op（新增 Python 级语法）
- 可按 kernel 开关（hook 设定后对其后编译的 kernel 生效）；插件自带 hashing 策略管理 kernel cache
- Meta 的 TLX 从实验 fork 变成独立包 `utlx` 并默认启用：`tlx.local_alloc / async_load / async_dot / local_view` 等显式共享内存与异步流水原语，H100 与 MI350 上持久化 GEMM 已达或超过厂商库
- 发布纪律：feature work 不允许 cherry-pick，只收回归修复、静默算错/崩溃/死锁类关键修复、新特性修复与「新兴硬件支持」
- 关联 [[AI编译器2026格局]] [[推理引擎格局-vLLM-SGLang]] [[MLX生态2026]]
