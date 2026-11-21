# 沙箱逃逸的真实类别：边界不在箱内，在出口的 trust handoff

- 来源: https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-coding-agent-sandbox-escapes-20260722-c
- 日期: 2026-07-22（Pillar Security「The Week of Sandbox Escapes」，2026-07-20 起逐日披露）
- 标签: #AI-Agent #Sandbox #安全 #LongLoop

## 摘要
Pillar 对 Cursor、Codex CLI、Gemini CLI、Antigravity 披露 7 个问题，没有一个是正面打穿沙箱：agent 全程守规矩，只写了一个「沙箱外某个受信组件稍后会读/跑」的文件（IDE hook 配置、venv 解释器、git config、task 定义）。沙箱管住的是 agent 的动作，不是它产出物的下游消费者。

## 要点
- 具体条目：Cursor hook 执行 CVE-2026-48124（CVSS 8.5，3.0.0 修复）；Codex CLI 白名单绕过「GitPwned」（0.95.0 修复并有赏金）；Antigravity 两条 Google 定性为「其它有效漏洞」但降级且当时未修；Docker socket 共享暴露两家都拒绝认定为需修漏洞
- 四类失效模式：①denylist 式沙箱（macOS Seatbelt）枚举不全 OS 攻击面 ②项目内配置文件被当作受信基础设施 ③GIT/任务运行器在回合结束后以用户身份执行 ④共享 daemon（Docker socket）绕过隔离
- 同期另一类：Cato 的 DuneSlide（CVE-2026-50548/549，CVSS 9.8）直接改工作目录与符号链接校验逻辑、覆盖沙箱 helper —— 攻击的是执行体本身，结果相同：一次未批准的注入 = 用户态代码执行
- 证据权重不均：7 条中 4 条属 Cursor，DuneSlide 也只打 Cursor；结论可推广的是「模式」，不是各家风险等同
- 工程判据：threat model 必须显式列出「沙箱外会消费 agent 产出物的全部组件」（hook 引擎、扩展、包管理器、CI、Docker daemon），并把它们纳入最小权限与签名校验
- 对 LongLoop Console：验收产物本身即被下游消费，产物写入路径应与执行路径同权审计
