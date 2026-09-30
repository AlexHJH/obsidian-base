# CAKE：让 agent 直接写硬件显式调度 IR，编译器与 agent 协同进化

- 来源: https://arxiv.org/abs/2608.12629
- 日期: 2026-08-12（NVIDIA + 卡内基梅隆；Zihao Ye / Tianqi Chen / Luis Ceze 等）
- 标签: #AI编译器 #内核生成 #Agent #MLIR #GPU

## 摘要
现有内核 agent 把编译器当黑盒，只拿得到报错、正确性与耗时；DSL 则要么隐藏关键调度决策，要么用难懂的 layout 代数暴露它们。CAKE 让 agent 直接书写 CAKE IR——一种带类型、硬件显式的调度表示，暴露 warp 角色、数据搬运、同步与流水线，并让 verifier 与 cost model 在编译前就能推理程序；更关键的是 harness 本身也是被优化对象：反复出现的失败会沉淀成新的 verifier 规则、IR 原语、代价模型标定与可复用战术。

## 要点
- B200 上 Flash-KMeans「隐藏实现 + 干净起点」对比（8000 万 token 预算）：CAKE IR 最佳候选达调优后 FlashML 基线的 1.144×，直接写 CUDA/PTX 只有 0.928×
- 真实收益：agent 生成的 Kimi Delta Attention 相对官方 FlashKDA 几何平均 2.05× 并通过端到端服务验证；带 dispatcher 的 KNN/KMeans 在 400+ shape 上 1.42–2.12×
- 分工原则：调度只声明「要发生什么」（资源、角色、生产者–消费者屏障），barrier 地址、相位位、TMEM 偏移、descriptor 编码与 warp 身份全由 lowering 推导
- 工程闭环：单 shape 演化 / 库级泛化 / dispatcher 三层分离，4 个内核改动已提上游 PR，覆盖 Ampere→Blackwell
- 关联 [[AI编译器2026格局]] [[MLSys2026论文精选]]
