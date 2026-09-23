# WSO2 Agent Manager 1.0 GA 与 AutoGen 退居维护模式：Agent 控制面成新战场
- 来源: https://wso2.com/about/news/wso2-launches-agent-manager , https://github.com/wso2/agent-manager
- 日期: 2026-09-16
- 标签: #AIAgent #控制面 #治理 #开源

## 摘要
WSO2 Agent Manager 于 9 月发布 1.0 GA（Apache 2.0），这是面向企业的 agent 开放控制面，可跨框架、模型与部署形态统一做发现、身份委派、策略护栏与可观测性。同期 Microsoft 把 AutoGen 降为维护模式，新项目统一导向 Microsoft Agent Framework。

## 要点
- 能力面：联邦式 agent 管理（cloud/on-prem/hybrid 单一控制面）、agent 强身份与访问委派（每步动作可认证/授权/审计）、集中式策略与护栏、生命周期与评估
- 形态：自托管（K8s/Helm）或 SaaS；开源版免费，订阅档提供支持与企业功能，明确打反锁定牌
- 框架收敛：AutoGen 只收 bug 与安全补丁，新特性全进 MAF（1.0 GA 2026-04）；存量 AutoGen 团队有迁移窗口与兼容性风险
- 判断：竞争重心从「编排框架」上移到「控制面 + 身份 + 审计」；选型应把控制面与框架解耦，别被单一框架绑定
- 关联 [[AI-Agent-2026格局]]
