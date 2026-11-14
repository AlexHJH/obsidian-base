# Triton 上游同时长三套 sanitizer：GSan 查竞态、FPSan 查重结合、ConSan 查多 CTA 同步
- 来源: https://github.com/triton-lang/triton/pull/12704、/pull/12723、/pull/12728、/pull/12725、/pull/12646（11-10–11-13 集中合入）
- 日期: 2026-11-13
- 标签: #Triton #MLIR #GPU编译 #正确性验证 #Sanitizer

## 摘要
Triton 在补齐「编译器自带验收工具」：GSan 用影子内存池抓 GPU 数据竞争，FPSan 用显式 barrier 区分浮点重结合/未融合 FMA 与真 FMA，ConSan 管多 CTA/集群级同步顺序。三套都在做同一件事——把「kernel 结果对不对」从人工读代码变成可插桩运行的检查。

## 要点
- GSan 结构是 10 个 1 TiB 池（#12728 提到 64 卡会耗尽 16 GiB buddy 容量）→ VA 预留改 8 TiB/池、合计 80 TiB，物理内存仍按需分配；#12723 另把影子地址改成相对 reservation base，GB300 只要求 2 MiB 对齐（原先要 16 TiB 对齐）
- 插桩成本实测（GB300，Gluon MoE、GPT-OSS-120B、4 字节池）：batch 128→8192 为 +0.4%–+3.7%，且插桩输出与未插桩逐位一致——成本可接受是这套能日常开的前提
- #12704 是性能修法：只读 acquire 原先在导入整份 vector clock 期间一直持着影子锁，多 CTA 抢同一 flag 时跨 SM/跨卡串行；改成「读完就放锁、用 system-scope 读写锁钉住发布者时钟缓冲」→ 4 卡 152 CTA 的 poll 35.15→13.29 ms（2.65×），代价是单卡 publish 慢 79%
- FPSan 走语义路线而非数值：`tl.fpsan.barrier(x)` 做确定性的非自同构变换（高位混入低位），使「重结合 / 未融合 FMA」在标记点暴露；`homomorphic_cast` 保留低位载荷，让窄化 cast 不破坏加减乘的等价性
- ConSan 目前只见到 AMD 侧一条（多 CTA kernel 的启动集群 barrier 同步）；细节未读，不足以判断覆盖范围
- 判读：这比「再写一个 pass」更贴 Alex 那条主线——`clc=True` 把持久化变成编译选项、三套 sanitizer 把验收变成编译选项，编译器边界正在从「生成代码」扩到「生成证据」
- 口径：全部来自 PR 描述与作者自测（单台 GB300、单机多卡），无第三方复现；FPSan 的行为语义我按 PR 文字推断，未跑过

关联：[[AI编译器2026格局]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[MLSys2026论文精选]]
