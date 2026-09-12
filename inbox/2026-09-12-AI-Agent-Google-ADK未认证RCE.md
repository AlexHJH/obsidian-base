# CVE-2026-79696：Google ADK 未认证 RCE（CVSS 10.0）
- 来源: https://www.ionix.io/threat-center/cve-2026-79696
- 日期: 2026-09-09
- 标签: #AI-Agent #安全 #CVE #GoogleADK

## 摘要
Google Cloud ADK for Python 2.0.0–2.6.0 的 `adk web` 存在代码注入：运行时装有 pytest 时，未认证远端攻击者可用构造的 test session replay 执行任意代码，CVSS 4.0 满分 10.0。

## 要点
- 根因 CWE-184「标准库黑名单不完整」，可绕过沙箱/白名单限制；影响 OSS Python、Cloud Run、GKE 上装了 pytest 的部署
- Google 已对云托管实例直接打补丁，自建（含本地 `adk web`）需自行升级
- 同一组件 2026-04 已有 GHSA-rg7c-g689-fr3x（1.7.0–1.28.1 未认证 RCE，类型相同）→ ADK Web 持续是稳定攻击面
- 提醒：Agent 框架的 research/dev 面（session replay、pytest、调试 UI）常被当作「非生产」而漏管，实为暴露面
- 关联 [[AI-Agent-2026格局]]
