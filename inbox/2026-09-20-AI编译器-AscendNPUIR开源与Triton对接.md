# AscendNPU IR 全面开源：用 Linalg 做 Triton 与昇腾硬件之间的接缝
- 来源: https://www.hiascend.com/developer/techArticles/20250529-1 , https://news.qq.com/rain/a/20260817A07PH600
- 日期: 2026-08-17
- 标签: #AI编译器 #昇腾 #MLIR #CANN #国产算力

## 摘要
华为把毕昇编译器的核心 AscendNPU IR（基于 MLIR 的昇腾 Tile 级中间表示）全面开源，并给出 Triton 接入路径：Triton IR 先降到硬件无关的 Linalg IR，再进昇腾专属方言编译到二进制，CANN 9.1 文档已设独立章节。

## 要点
- 分层结构：内部含 HFusion（高层语义预处理与算子简化）与 HIVM（昇腾亲和优化）两层方言，向下对接 LLVM IR、最终生成昇腾机器码；对外是面向三方框架的 MLIR 接入层
- 硬件表达：把计算、搬运、同步抽象成高阶 OP，由毕昇编译器自动做 CV 架构映射、片上内存管理与多处理单元流水并行；官方称中端优化使算子性能提升 20% 以上
- 分层开放：既能用 Tensor 级 OP 快速适配，也能下沉到细粒度指令集 OP 深度优化，还支持自定义算子融合后回吐 IR——学术与自定义硬件场景可直接基于 IR 做研究
- Triton 接缝：新增对社区 MLIR 的转换支持，以 Linalg 方言作硬件无关层承接 Triton IR，再降为 AscendNPU IR（覆盖 A2/A3 到昇腾 950）；对自研 GP+CC 异构 SoC 的启示是「把接缝放在社区认可的硬件无关方言上」，而非自造前端
- 生态位置：与 CANN 9.1（含 Mooncake / vLLM / SGLang 适配、HIXL 单边通信）配套，国内 Triton 类生态（FlagTree/FlagOS、TileLang-Ascend）同步跟进
- 观察点：开源范围是「编译器 + 虚拟指令集接口」，文档与代码仓分处昇腾社区与 GitHub，版本节奏与产品上市同步——需以实际仓内 commit 而非发布会口径判断成熟度
- 关联 [[AI编译器2026格局]] [[AI芯片推理硬件2026全景]] [[MLSys2026论文精选]]
