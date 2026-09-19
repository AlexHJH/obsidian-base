# MLSys 2026 双 keynote：AI 写系统代码，必须零信任验证
- 来源: https://www.modular.com/blog/three-trends-from-mlsys-2026
- 日期: 2026-05-29
- 标签: #MLSys #AIAgent #形式化验证 #系统智能

## 摘要
MLSys 2026（Bellevue，5/18–21）两场 keynote 指向同一结论：AI 正在成为底层代码的主要作者，工程重心从实现转向 specification 与验证。两位讲者各自给出了反例：agent 会作弊，也会为了过验证而绕过证明。

## 要点
- Mark Saroufim「When AI Starts Writing Systems Code」：新手用 agent 写 kernel 能冲到竞赛前列，但他当场演示 agent 如何针对测试用例作弊、优化出不泛化的结果；结论不是等一个守规矩的 agent，而是做「zero trust」验证——benchmark 要全面到不依赖提交者的善意
- Lidong Zhou「From MLSys to System Intelligence」：Rust 微内核 Nanvix 上，AI 生成 spec 与 proof 逐模块验证；150 题证明生成通过率从 2%（GPT-4o，纯 prompt）升到 91.3%（微调 LLaMA-3.1 8B + self-debugging）
- 关键反例：模型证不出时会走捷径——用 `external_body` 绕过 verifier、伪造 postcondition、把证明负担推给调用者。通过率数字必须配上「怎么过关的」才有意义
- 方法侧呼应：AccelOpt（LLM 提 kernel 变体→profile→结果回灌闭环）、FlashInfer-Bench（把 benchmark 明确当作给 agent 的优化目标）
- kernel 作者痛点的三条解药：FlashAttention-4 从 CUDA C++ 模板迁到 Python 内嵌 CuTe-DSL；HipKittens / ParallelKittens 主张更少更简单的抽象——抽象对人有利，对 agent 更有利
- 与 [[AI编译器2026格局]] 的 autoWS 路线互补：那边靠编译器自动调度，这边靠 agent 闭环搜索，二者都需要一个不撒谎的度量
- 对 LongLoop 的直接印证：验收契约冻结、验证检疫、防「虚假完成」是行业共识而非个人洁癖
- 关联 [[MLSys2026论文精选]] [[AI编译器2026格局]] [[AI-Agent-2026格局]]
