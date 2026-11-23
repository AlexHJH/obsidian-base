# Triton 3.7 插件扩展系统：TLX 从 Meta 私有 fork 变成上游可加载插件
- 来源: https://pytorch.org/blog/triton-plugin-extensions-enabling-tlx-and-custom-compiler-passes-out-of-the-box
- 日期: 2026-07-15
- 标签: #AI编译器 #Triton #MLIR #插件化 #TLX

## 摘要
PyTorch-Triton 3.7 引入 Plugin Extensions：`TRITON_PLUGIN_PATHS` 指向 .so，即可在运行期向 NVIDIA/AMD 后端注入自定义 pass、整个 MLIR dialect 以及顶层 DSL 语法，无需 fork 或重编译 Triton。Meta 的 TLX（显式 shared memory 分配 + async load/dot 流水）成为首个官方消费者，装机即用。

## 要点
- 钩子埋在 `backend/compiler.py` 各 stage：任意位置插 pass、禁 pass、替换 pass（如自定义 warp specialization）、甚至整段 pipeline 覆盖；可 per-kernel 开关，kernel cache 的 hash 策略由插件自己负责（utlx 已封装）
- 抽象层级三档：单 pass → 独立编译的 dialect + conversion → 新 DSL op（Python 层语义扩展），最后这档才是"不改 Triton 就造新编程模型"的口子
- H100 FP16：Triton+TLX 对 cuBLAS 257.0 vs 247.8（128×13312×16384）、566.7 vs 549.4、575.9 vs 564.8 TFLOPS，最大形状是 −0.9% 到 +3.7%；MI350 对 rocBLAS 全面 +11.8%~+15.2%
- GPU Mode Trimul 复合管线（5 个 projection GEMM + batched matmul + LN/sigmoid/permute）：19.2ms → 12.0ms，1.61× 于 cuBLAS+torch.compile
- 判读：这是"厂商扩展怎么进入上游"的模板 —— 对自研后端（RISC-V 矩阵扩展一类）而言，与其长期维护 fork，不如把 layout/指令选择做成 dialect + pass 插件；代价是要自己承担 cache hash 与 hook 语义稳定性
- 关联 [[AI编译器2026格局]] [[NVIDIA-Tensor-Core-TMA与AI指令体系]]
