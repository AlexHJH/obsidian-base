# Weave：MoE megakernel 里做细粒度动态 SM 调度（arXiv 2609.21483）
- 来源: https://arxiv.org/abs/2609.21483
- 日期: 2026-09-18
- 标签: #MoE #megakernel #GPU调度 #LLM推理 #系统论文

## 摘要
专家并行下的 MoE 层是一次带昂贵 dispatch/combine 通信的分布式计算，现有 overlap 方案把 SM 静态切成「通信组 / 计算组」，但最优切分随每层路由结果与各 GPU 状态变化——固定策略不是浪费 NVLink 带宽就是浪费算力。Weave 在常驻 megakernel 内按层、按 GPU 运行时重切 SM。

## 要点
- 关键观察：路由一旦算完，本层的通信量与计算量就是已知的，因此可以「预测后调度」而非拍脑袋静态划分
- 空间调度器按通信/计算吞吐比把 SM 切成两类 worker，时间调度器协调两组以最小化 SM 空闲（消 MoE 数据依赖造成的 bubble）
- 4×H100 SXM 上六种主流 MoE 模型：MoE 层几何均值加速 2.89×，端到端几何均值加速 1.33×（对比五个 SOTA 基线）
- 与 [[AI编译器2026格局]] 的 megakernel 议程互为印证：任务/角色能在 kernel 内被调度，正要求 kernel 本身暴露可缝进大程序的 IR 结构
- 关联 [[MLSys2026论文精选]] [[AI编译器2026格局]] [[推理引擎格局-vLLM-SGLang]]
