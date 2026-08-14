# OpGuard：位对齐调试生产级 LLM 训练（OSDI'26 Best Paper 提名）
- 来源: https://orderlab.io/OpGuard
- 日期: 2026-07-13
- 标签: #MLSys #OSDI26 #LLM训练 #调试 #字节跳动

## 摘要
UMich OrderLab × ByteDance Seed 提出"位对齐"（bitwise alignment）作为 LLM 训练的正确性 oracle 与调试原语：跨异构训练栈发现语义稳定算子边界并打轻量指纹，找出两轮执行首个位不一致的 tensor 作为调试枢轴。

## 要点
- 已部署字节预训练/后训练，诊断 20+ 生产问题（kernel 竞态、静默数据损坏），调试时间从数天缩至数分钟
- 无需"黄金基线"即可比较；通过控制良性非确定性，让首个不匹配点成为强错误证据
- 关联 [[MLSys2026论文精选]]
