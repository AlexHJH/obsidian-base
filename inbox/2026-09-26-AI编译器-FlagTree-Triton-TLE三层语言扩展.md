# FlagTree / Triton-TLE：给 Triton 装三层「语言扩展」，把芯片差异收敛进编译器
- 来源: https://news.qq.com/rain/a/20260807A0B27800 , https://zhuanlan.zhihu.com/p/2048539180876163013 , https://segmentfault.com/a/1190000048181912
- 日期: 2026-08-07（智源 FlagOS 社区发布）
- 标签: #AI编译器 #Triton #MLIR #国产芯片

## 摘要
智源研究院与 FlagOS 社区在统一 AI 编译器 FlagTree 上提出 Triton-TLE（Triton Language Extensions）：不推翻 Triton，而是按开发者专业度分三层渐进扩展表达能力，用统一硬件 IR 把芯片差异收敛进编译器内部。2026 年 7 月 NVIDIA 团队又把 CUDA Tile IR 接入 FlagTree，使 FlagTree 写出的算子在 NVIDIA 新 GPU 上获得明显提升。

## 要点
- 三层设计：TLE-Lite（算法工程师，一次编写多后端运行，原生 device_mesh 分布式）→ TLE-Struct（性能优化工程师，架构感知调优：tile/slice 语义、开放 warp/cluster 并行与更多存储层次）→ TLE-Raw（底层开发者，内联 CUDA/MLIR 等厂商原生代码）；Lite 保持 Triton 原抽象不变，向后兼容
- 编译链路：Triton-TLE → FLIR（FlagIR，承担 TLE 降级并对接厂商 IR 如 AscendNPU IR）→ LLVM IR；TLE-Raw 则走厂商私有编译器再 Lower 到 LLVM IR
- 对比表口径：相比 OpenAI Triton / TileLang / cuTile，TLE 的差异项是「保留 Python DSL + 增加 tile/slice 语义 + 15+ 后端统一编译 + 分布式原生支持」，其余抽象层级按 Lite/Struct 分级开放
- 已知边界（二手信息，未见论文）：TLE-Raw 仍在实验阶段、依赖自定义 LLVM 构建；DSA 后端（昇腾/清微）的 TLE-Struct 支持停在 Triton 3.2/3.3，未跟上 3.6；上游合入与社区渗透率仍在推进，中文文档稀缺
- 与本库既有判断一致：[[AI编译器2026格局]] 记录的「Triton 作为通用前端 + 后端可扩展」路线，此处是国产统一编译栈的具体落法
- 关联 [[AI编译器2026格局]]、[[AI芯片推理硬件2026全景]]
