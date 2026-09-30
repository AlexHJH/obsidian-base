# SOSP 2026：llmovoice 把语音 LLM 的「上下文预算」做成中间件

- 来源: https://arxiv.org/html/2609.04288v1 , https://llmovoice.com/research , https://gc.sjtu.edu.cn/off-the-press/2026-09-09/164170
- 日期: 2026-09-30（论文 arXiv 2609.04288 于 9/3 提交，DOI 10.1145/3830418.3843860；SOSP 2026 会中 9/29–10/2 布拉格宣讲）
- 标签: #SOSP2026 #语音LLM #上下文管理 #Harness #MLSys

## 摘要
上交（Yifei Zhu 组 / AgenticSys）的 llmovoice 是夹在流式音频与模型推理之间的上下文管理中间件：把语音对话显式建模成 VoicePage / VoiceThread 两层结构，再按上下文预算动态决定「拿哪些历史、以什么保真度（音频/转写/摘要）喂模型」，并把语速等副语言信息与网络状况等环境信息一并纳入决策。

## 要点
- 两个抽象：VoicePage（一次完整问答，含 audio/transcript/summary/style/环境状态）+ VoiceThread（跨页的任务与话题连续性），一页可同时属于多个 thread（如一句既进旅行规划线也进语言学习线）
- 关键区别：不把副语言（语速）与环境（网络抖动）留在音频里当隐变量，而是显式状态参与预算分配——这是「语音 agent 记忆」区别于文本 RAG 的地方
- 决策分两层：Context Projector 选历史 + 表示保真度；Context Orchestrator 统一决定内容、表达风格与轮次控制（turn-taking），每轮结束提交为新 vpage 闭合回路
- 官方口径：长对话单轮成本最高降 40.1×、长期语音服务整体成本降 79.2%、保留基线答案质量 98.7%、语速对齐误差降 52.35%、网络波动下误触发改善 2.29×（自测口径，未第三方复核）
- 对本机的意义：语音服务的瓶颈不在 ASR/TTS，而在「历史怎么压缩、以何保真度回喂」——与常驻会话管理的设计同构，可直接对照本机语音栈的会话状态处理
- 关联 [[MLSys2026论文精选]]、[[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]
