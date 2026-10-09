# Harness Engineering 正式化：Agent = Model + Harness
- 来源: https://www.augmentcode.com/guides/harness-engineering-ai-coding-agents ; https://openai.com/index/harness-engineering
- 日期: 2026-02-11
- 标签: #AI-Agent #Harness #上下文工程 #工程化

## 摘要
「Harness Engineering」在 2026 年初被正式命名。OpenAI 的 Ryan Lopopolo（2/11）以其「零手写代码交付生产应用」的实践定调 **Humans steer. Agents execute.**，LangChain 把它压缩成 **Agent = Model + Harness**。竞争焦点由此从提示词（2022）→ 上下文检索（2023–24）推进到 2025+ 的「让 agent 可靠运行的系统工程」。

## 要点
- 定名脉络：Mitchell Hashimoto《My AI Adoption Journey》（2026-02 初）与 OpenAI 博文（2026-02-11）并列源头；Karpathy 贡献 context engineering（2025-12-19）与 agentic engineering（2026-02）
- 常见误诊：大量 2025 生产故障其实是 harness 故障被当成 prompt/context 故障，修错层 → 症状复发 → 团队对 agent 失去信心
- 硬证据：Apiiro 分析显示 2025-06 单月 AI 生成代码引入 >10,000 条安全发现（较 2024-12 增约 10×）；Spotify Honk 靠验证回路累计合并 1,500+ AI PR
- 对 LongLoop 的映射：harness 的「约束 + 验证回路」正是本库「验收契约冻结 + 验证检疫」的通用化表述
- 关联 [[AI-Agent-2026格局]]、[[MLSys2026论文精选]]
