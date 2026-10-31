# MLIR 成了 AI 栈的腰部：Qualcomm 39 亿美元买的是控制点
- 来源: https://www.thesoftwarefrontier.com/p/exploring-how-mlir-works-the-compiler
- 日期: 2026-07-15
- 标签: #MLIR #AI编译器 #Triton #Modular #Qualcomm

## 摘要
一篇长文把 MLIR 从 dialect/pass 机制讲到产业格局：Qualcomm 于 2026-06-24 宣布以约 39 亿美元全股票收购 Modular（约 150 人，人均近 2600 万美元），特斯拉 FSD v14.3 宣称编译器与运行时「用 MLIR 从零重写」，作者认为 MLIR 已是 AI 编译的共享腰部，而买点不是营收而是控制点。

## 要点
- 落点证据：PyTorch 2 之后 Inductor 默认发射 Triton，而 Triton（2022 起基于 MLIR 重建）经 TTIR/TTGIR 到 ptxas——「大多数被编译的 PyTorch GPU 负载今天都路过 MLIR」，只是使用者不知道
- 前端分层固化：StableHLO 作可版本化序列化层（JAX/TF/PyTorch 可换编译器）、IREE 是端到端 MLIR-native、AMD MLIR-AIE 1.2（2026-01）供 Ryzen AI NPU、Qualcomm Hexagon-MLIR（arXiv 2602.19762）同时吃 PyTorch 图与 Triton kernel
- 2025 年后 Gluon 把 TTGIR 级控制（layout、smem、warp specialization）当产品暴露，等于承认「高层 Triton 不足以榨干硬件」，但复用同一 dialect 栈
- 结构性弱点：core MLIR 没有跨 release 的稳定 IR 契约，下游每版 LLVM 都要交 rebase 税（Triton、IREE 为此养人头）；Lattner 的批评与社区 charter/治理改革仍在进行中，不是已解问题
- NVIDIA 的站队逻辑：贡献 Triton NVIDIA 后端 + CuTe DSL 建在 MLIR 上，换来「自家硬件是共享栈的最佳目标」，同时把 ptxas、SASS 调度、NVLink 库继续闭源——利润护城河上移
- 竞争含义：vLLM/SGLang 经 Triton、IREE、MAX 都落在同一腰部，B300 与 MI400 的 kernel 层切换成本被系统性拉低
- 口径：产业数字取自 Qualcomm 新闻稿＋Reuters/WSJ/NAND Research 转述，作者复算人均价；文中 IR walkthrough 为教学示例，不含自建基准

关联：[[AI编译器2026格局]]、[[推理引擎格局-vLLM-SGLang]]
