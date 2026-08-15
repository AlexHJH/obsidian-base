# Claude Platform 8 月更新：推理挂钩 + Cowork/Claude Code 合规转录
- 来源: https://platform.claude.com/docs/zh-TW/release-notes/overview
- 日期: 2026-08-11
- 标签: #AI-Agent #Claude #企业合规 #安全

## 摘要
Claude Enterprise 8 月密集上线治理能力：Inference hooks 让每个受管控 prompt 在推理前等待组织 AI 安全服务器的 allow/deny 判定；Compliance API 可导出 Cowork 与 Claude Code 会话逐字记录（本机 + 远程）。同时退役 Claude Opus 4.1、统一升级 Opus 5。

## 要点
- Inference hooks（8/5 Beta）：请求经签名、失败处理可配置，每次拒绝记录进合规活动摘要
- Compliance API（8/11 Beta）：`sessions/local` 系列端点返回本机 Cowork/Claude Code 会话逐字记录（8/3 已支持远程会话）
- Claude Opus 4.1（claude-opus-4-1-20250805）8/5 退役，API 请求返回错误，建议升级 Opus 5

相关: [[AI-Agent-2026格局]]
