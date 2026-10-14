# ClangIR 被接受「默认构建」：MLIR 成为 Clang 的常规依赖，代价是构建时间翻倍
- 来源: https://maskray.me/blog/clangir-and-the-cost-of-mlir ; https://www.phoronix.com/news/ClangIR-Build-Default-Maybe ; https://freenode.net/article/rfc-would-enable-clangir-by-default-at-steep-build-cost
- 日期: 2026-10
- 标签: #AI编译器 #MLIR #LLVM #ClangIR

## 摘要
ClangIR（CIR）是插在 Clang AST 与 LLVM IR 之间的 MLIR dialect，2026 年 10 月 clang area team 接受将其「默认构建」。争议不在功能而在工程成本：MLIR 从此成为 Clang 的常规依赖，社区估计 Clang 自身构建时间翻倍以上，且成本在各子项目间分布不均。

## 要点
- CIR 的价值是在 AST 与 LLVM IR 之间保留高层 C/C++ 语义，优化不必等到 LLVM IR 才做
- RFC 的实质要求有两条：MLIR 作为默认依赖 + CIR 测试进入默认测试集，讨论焦点是构建成本
- 对 MLIR 生态的含义：MLIR 正从「AI/方言基础设施」变成通用编译器前台的默认依赖，被 AI 编译器之外的主流工具链绑定
- 证据边界：构建时间「>2×」为社区讨论中的估计值，未读到官方构建基准
- 关联 [[AI编译器2026格局]]、[[MLSys2026论文精选]]
