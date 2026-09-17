# OSDI'26：RL 后训练被拆成独立的系统问题
- 来源: https://paper.lingyunyang.com/reading-notes/conference/osdi-2026
- 日期: 2026-09-17
- 标签: #OSDI #RL后训练 #集群调度 #推理引擎

## 摘要
OSDI'26 出现 6 篇 RL 后训练系统论文（Weave/RollArt/RLinf/DynaRL/Seer/RobustRL），共同前提是 rollout（带宽密集）与训练（算力密集）物理分离到各自集群。分离后的依赖气泡、硬件错配与长尾延迟，成为与算法正交的独立系统议题。

## 要点
- **Weave**（HKUST + UIUC + 阿里）：气泡本质是「一个作业的结构性闲置可被另一作业的活跃阶段填满」；co-execution group 抽象把集群切为隔离的 locality domain，组间用保守随机规划做作业放置、组内用可证明最优的轮转调度，并靠 residency 约束把巨型模型状态驻留主机内存实现 warm-start 切换；328 H20 + 328 H800 上成本效率 1.84× 于标准分离、1.38× 于最佳共置，100% SLO
- **RollArt**（HKUST + 阿里通义）：按阶段匹配硬件——prefill 走算力优化 GPU、decode 走带宽优化 GPU、环境执行走 CPU 集群；rollout 在轨迹级解耦（生成/环境交互/打分互不阻塞），stateless reward 卸载到 serverless，staleness-bounded 异步权重同步；训练时间降 1.31–2.05×，阿里 3000+ GPU 上训百亿级 MoE 供 Qoder
- **Seer**（月之暗面 + 清华）：同 prompt 请求的输出长度与响应模式高度相似 → divided rollout 动态负载均衡 + context-aware 调度 + 自适应分组投机解码；rollout 吞吐最高 2.04×，长尾延迟降 72–94%
- **RLinf**（清华/无问芯穹等）：M2Flow（macro-to-micro flow）把 RL 工作流在时间与空间两维自动分解重组，配上下文切换与弹性流水线，端到端吞吐 1.07–2.43×
- 关联 [[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]] [[AI编译器2026格局]]
