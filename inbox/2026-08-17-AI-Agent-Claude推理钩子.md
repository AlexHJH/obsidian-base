# Claude 推理钩子 (Inference Hooks) 上线 Enterprise Beta
- 来源: https://platform.claude.com/docs/zh-CN/release-notes/overview
- 日期: 2026-08-05
- 标签: #AI-Agent #Claude #安全治理 #Enterprise

## 摘要
8/5 Anthropic 为 Claude Enterprise 推出「推理钩子」Beta：将 Claude 指向组织自己的 AI 安全服务器，claude.ai、Cowork、Claude Code 中每个受管控提示都在推理继续前等待服务器 allow/deny 裁定。

## 要点
- 请求经签名、故障处理可配置，每次拒绝写入合规活动信息流；安全审核从「事后审计」进到「推理前置拦截」
- 呼应 Anthropic 三态安全模型（block/allow/triage），把推理本身变成可挂载安全策略的扩展点
- 同批停用 Claude Opus 4.1，官方建议升级 Claude Opus 5
- 关联 [[AI-Agent-2026格局]]
