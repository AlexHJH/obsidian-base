# Claude Managed Agents 放开「动态工作流」+ web 工具域限制防外泄

- 来源: https://platform.claude.com/docs/en/release-notes/overview
- 日期: 2026-10-09（动态工作流）/ 2026-10-07（web 工具限制）
- 标签: #AI-Agent #Anthropic #多智能体 #工作流 #安全

## 摘要
Claude Managed Agents 新增动态工作流（beta）：agent 可自己写「多阶段跑多个 agent 再合并结果」的程序，由服务端在后台作为 workflow run 执行。同期把 web_search / web_fetch 收进域白名单，并让 web_fetch 只能抓会话内已出现过的 URL。

## 要点
- 开启方式：agent 的 `multiagent` 字段设为 `{"type":"multiagent_20261001","workflows":{"type":"enabled"}}`，beta header `managed-agents-2026-04-01`；进度靠 session 事件流的 `workflow_run.*` 事件追踪
- 意义：编排从「平台固定图 / handoff」转向「agent 运行时自己生成编排程序」，但执行落在服务端后台而非沙箱内
- web_fetch 只接受会话内已出现过的 URL（用户消息文本 / web_search 结果 / 先前 fetch 页面），否则返回 `url_not_in_prior_context`；仅出现在模型输出、system prompt、附件或 bash/MCP 工具输出里的 URL 不算 → 针对数据外泄
- 受限网络环境下 `allowed_hosts` 同时约束 web_search / web_fetch，`allowed_domains` 超出 `allowed_hosts` 会让建会话 400；`allowed_hosts` 条目需精确匹配，`*.` 才是子域通配
- 顺带（10-07）：Sonnet 5.5 prompt cache 读价 $0.20→$0.10 / 百万 token（0.05× 输入价）；Haiku 5.5 发布（1M 上下文、adaptive thinking，手动 thinking 预算与 temperature/top_p/top_k 会 400）
- 口径：官方 release notes 原文，未经第三方复核；本地记录为原文摘录
- 关联 [[AI-Agent-2026格局]]、[[LongLoop Console]]
