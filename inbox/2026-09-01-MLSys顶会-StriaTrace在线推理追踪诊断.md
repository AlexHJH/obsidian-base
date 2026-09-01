# StriaTrace：在线 LLM 推理的低开销追踪诊断 (OSDI'26)
- 来源: https://www.usenix.org/conference/osdi26/presentation/wu-haonan
- 日期: 2026-09-01
- 标签: #MLSys #OSDI #大模型推理 #可观测性

## 摘要
上交 × 阿里提出 StriaTrace，面向在线 LLM 推理的追踪诊断系统。推理服务受细粒度 SLO 约束，偶发性能异常即可违约，而现有工具追踪开销过大、训练向诊断工具难以捕获偶发异常。

## 要点
- 三原则：只追关键同步点、只追关键路径、仅异常时详细追踪
- 动态回归 roofline 模型 + 相关性诊断定位根因
- 追踪开销较替代方案降 97.8%，已在阿里生产落地，诊断 19 类根因数百次异常
- 关联 [[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]]
