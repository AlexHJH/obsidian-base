# 用 Agent 给 triton-cpu 加 qemu-riscv64 后端：六段 pass 管线与交付纪律
- 来源: https://zevorn.cn/posts/48
- 日期: 2026-05-16
- 标签: #Triton #RISCV #MLIR #Agent #编译器

## 摘要
作者以"RISC-V 知识 + agent 补 Triton 编译器盲区"的方式，从零给 triton-cpu 加了 qemu-riscv64 后端：TTIR → TTCIR → TTTCIR → LLVM dialect → RISC-V asm → .so，自写 `ConvertDotToRVV` 与 SLEEF RVVM1 数学库分派。四个 plan、66 任务、29 条 AC、18 个 milestone，规划 3 天（4/27–29），执行每 plan 约 3 小时 / 9 轮。

## 要点
- 关键新 pass：`ConvertDotToRVV` 把 `cpu::DotOp [M,K]x[K,N]` 按行展开成向量 FMA（LLVM 落到 `vfmacc.vf`），与 x86 的 `ConvertDotToFMA` 共用同一套高层策略，只换下标量/广播细节
- `tl.load` 的 mask 语义在 `make_ttcir` 被降成 `llvm.masked.load`，再由 RVV 后端变成 `vle32.v + vmerge`
- ABI：kernel 参数后固定追加 pid0/1/2 + gridX/Y/Z 六个调度参数；grid 展开顺序 (z,y,x) 与 CUDA 一致，外层用 `#pragma omp parallel for schedule(static)` 承载 program_id
- Git 两层纪律值得抄：开发分支按 `Round N` 保留全迭代历史，main 上 squash 成"每 commit 可独立 build/test"的特性提交 → 可 bisect、可 cherry-pick
- QEMU 能力边界说清了：qemu-user 能验多线程正确性，**验不了 RVWMO 内存序**（host 内存模型会掩盖 relaxed 排序 bug）
- 验证分级：单算子 → Softmax（单遍 reduce）→ LayerNorm（多遍 reduce）→ MLP（多 kernel 组合），容差 atol 从 1e-4 放宽到 0.1
- agent 边界：擅"已知模式的高质量变体"，不擅未知空间的原创探索；16 次 Codex 评审抓出 7 个盲点（x86 硬编码 `opts.x86=true`、cache-key、OpenMP 验证方法等）
- 与 [[AI编译器2026格局]] 衔接；这批 pass 结构与我们在 Triton→RISC-V AI-CPU 栈上要做的形态一致
