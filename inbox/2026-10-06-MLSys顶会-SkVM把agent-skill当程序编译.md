# SkVM：把 agent skill 当程序编译，跨模型/跨 harness 复用（SOSP 2026）
- 来源: https://arxiv.org/abs/2604.03088 ; https://github.com/SJTU-IPADS/SkVM ; https://ipads.sjtu.edu.cn
- 日期: 2026-04（arXiv v1）；SOSP 2026 录用（IPADS 于 2026-07 公布）
- 标签: #MLSys顶会 #SOSP #AI-Agent #AI编译器 #skill

## 摘要
上交 IPADS 分析 10 万+ skill 后发现「skill 文本与底层 LLM/harness 能力不匹配」是普遍失效原因，于是把 skill 当代码、把 LLM+harness 当异构处理器，做了一套编译与运行时（SkVM）：AOT 按模型能力画像生成适配变体并做环境绑定，JIT 做代码固化与反馈式改写。官方口径在 8 个 LLM × 3 个 harness 上任务完成率 +~10%、token 最多 -40%、端到端延迟最高降 19–50×。

## 要点
- 三类失配：模型失配（能力缺失需补偿/替换）、系统失配（工具与环境依赖漂移）、结构失配（串行工作流里可并行/向量化的部分没被提取）
- 编译三档对应传统编译：解释执行（现状，skill 原文直接喂模型）→ AOT（能力画像 + 环境绑定）→ JIT（自适应重编译）
- 关键机制 JIT-boost code solidification：把验证过的 skill 轨迹固化成可执行代码，这是延迟降一个量级以上的来源
- 对本机的意义：与 LongLoop「skill 化 + 固化」同构；延迟/token 收益属作者自测口径，跨 harness 泛化性需自己复现
- 同组 SOSP 2026 另有 Taming Dynamism on GPUs（已归档）、ProbeFS（DNA 文件系统）、StarfishOS（CXL 单系统镜像）——编译器视角的 agent 化是 IPADS 的主线
- 关联 [[AI编译器2026格局]]、[[AI-Agent-2026格局]]
