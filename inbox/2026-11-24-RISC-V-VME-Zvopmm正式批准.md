# VME 正式批准（Zvopmm）：BoD 11-18 通过，但工具链还没跟上
- 来源: https://riscv.atlassian.net/wiki/spaces/VMEX/pages/663617995/Ratification+Plan ；https://neuling.org/riscv-software-news/riscv-software-news-2026-11.html ；https://developers.google.com/coral/guides/hardware/vme-matrix-engine
- 日期: 2026-11-18
- 标签: #RISC-V #VME #Zvopmm #矩阵扩展 #工具链

## 摘要
VME TG（规范名 Zvopmm）走完了时间线：Freeze 9-25、公开评审 10-09、TSC 11-04、BoD 11-18 批准；11 月的 RISC-V Software News 已把它从 spec pipeline 移出（原状态 Freeze），计入 100 项规范中 59 项已批准。但同月上游提交里没有一条 Zvt 代码——标准批准与可编译是两件事。

## 要点
- 语义：两个 vector 寄存器做外积，累加进新增的 matrix accumulator regfile；尺寸无关（类 vsetvl 的配置机制），设计点可从高端铺到低端
- 三条路的分野：IME 只用 RVV 寄存器堆（嵌入式/A 类受容量限制）、AME 是独立二维 tile 寄存器（HPC 类）、VME 与 hart/RVV 紧耦合 + 独立累加器，靠少数据搬运换能效
- 已实现先于标准：Coral NPU 的 Zvt 引擎按 v0.4 实现（`mset` 配 M×K×N tiling 后发 systolic 微操作），官方 v1.0 文档计划 2026 年底
- 上游节奏（11 月前 20 天）：LLVM 64 / GCC 25 / OpenSBI 11 提交，扩展相关落在 Zfinx、Zhinx、Zicond；Zilx 从 Stabilization 进 Freeze
- 姊妹提案状态：IME 家族（Zvvmm/Zvvfmm/Zvvmtls/Zvvmttls）仍在 Freeze 0.9（2026-08-04 release），Ratification-Ready 0.99 未出
- 判读：批准只解锁「可以宣称符合」，代码生成要靠 LLVM/GCC 的 MC 层 + intrinsic，这段通常滞后一个 release 周期；厂商扩展（Xsfvfw / 玄铁 v0.6）反而是眼下唯一能跑的工具链路径
- 关联 [[RISC-V矩阵扩展两年进展-2024-2026]]、[[2026-11-22-RISC-V-VME-Zvopmm获批]]
