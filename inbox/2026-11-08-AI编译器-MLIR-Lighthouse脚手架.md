# MLIR 外面长出一层脚手架：Lighthouse 一周年，改行当上游金丝雀
- 来源: https://discourse.llvm.org/t/mlir-project-lighthouse-update/91790
- 日期: 2026-09-11（EuroLLVM 2026 技术报告 2026-04，讲者 Renato Golin / Intel PCL）
- 标签: #MLIR #编译器基础设施 #Triton #上游社区

## 摘要
Lighthouse 是 2025 年进 LLVM 家族的新项目，定位不是再造一个编译器，而是包在 MLIR 外面的一层 Python 脚手架：把 MLIR Python bindings 里要反复手写的流程收成模块，让研究者用几十行 Python 拼出端到端 pipeline 并跑 benchmark。一年后它承担起第二份工作——当上游 MLIR 的金丝雀：测试留在上游，CI 循环放在下游，每晚对着 MLIR nightly 重新编译，验证「我们以为的 pass 行为」还成立。

## 要点
- README 记为四个组成部分，核心是 Python 模块层 Lighthouse——把 MLIR Python bindings 里的胶水逻辑固化，而不是新增 dialect
- 跑法与基准：`driver.run()` 拿 module，按 numpy 形状填 KernelArgument，`Runner` 负责 execute/benchmark（含 warmup），封装在 `lh-run` 工具里
- 测试分工已形成共识：LIT、单 pass/单 dialect 的细粒度测试留 MLIR 本体，端到端测试放 Lighthouse
- Intel PCL 的用法值得抄：拿 KernelBench 当公开负载，提取公共 pipeline 再反哺上游 pattern/transform，避免每个下游各搭一遍
- 现状偏早期：Compiler Explorer 式「输入 MLIR + schedule 看后端结果」只搭了框架、没有用户；KernelBench demo 被当成给别人起步的模板
- 口径：EuroLLVM 议程、Intel 讲稿 PDF 摘录与 discourse 摘要拼成；本机 web_extract 被爬虫代理拦截，未读讲稿全文

关联：[[AI编译器2026格局]]、[[RISC-V矩阵扩展两年进展-2024-2026]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
