# 把 Claude Code 拆开数代码：1.6% 是 AI 决策，98.4% 是 harness
- 来源: https://arxiv.org/abs/2604.14228 （VILA-Lab/MBZUAI，Jiacheng Liu 等；代码 github.com/VILA-Lab/Dive-into-Claude-Code；采集 2026-11-18）
- 日期: 2026-04-14
- 标签: #AI-Agent #Harness #ClaudeCode #上下文工程 #权限模型

## 摘要
研究团队逆向了 Claude Code v2.1.88 的 TypeScript 包（1,884 个文件、约 51.2 万行），按功能分类后给出一个可量化的结论：只有约 1.6% 的代码是 AI 决策逻辑，其余 98.4% 是确定性基础设施——权限门、上下文管理、工具路由、错误恢复。核心 agent loop 就是一个 `while(true)`：调模型、解析 `tool_use`、过权限、派发工具、把结果塞回去。论文把它概括为「minimal scaffolding, maximal operational harness」，并与开源 Agent 系统 OpenClaw 做了同题异解对照。

## 要点
- 复杂度落点可测：五个价值（人类决策权、安全、可靠执行、能力放大、上下文适应性）→ 十三条设计原则 → 具体实现，而不是靠架构图宣讲
- 安全边界是「推理与执法分离」：模型只输出结构化 tool_use，不直接碰文件系统/shell/网络；harness 校验后执行，权限系统有 7 种模式外加一个 ML 分类器，默认 deny-first（deny > ask > allow，最严规则胜出）
- 上下文是真正的约束：五层压缩流水线按「渐进降级」触发——最便宜的一层先上，不够才升级侵略性；这一层是长任务不退化的关键
- 单一 `queryLoop` 同时服务 CLI / SDK / IDE，避免多入口行为漂移；CLAUDE.md 形成分层配置 + 渐进式扩展（trust trajectory）
- 判读：这条结论和「harness 决定 token 成本、模型只决定天花板」互为佐证——harness 不是胶水，是产品本体；也因为模型是商品，只在 scaffold 上堆逻辑的系统下次模型升级就会被洗掉

## 口径提醒
1.6% 是**代码行数**占比，不是算力或价值占比——模型仍承担理解与规划，harness 只让这些能力变得可执行；把「98% 不是 AI」当结论会误读。数据来自社区对反混淆后源码的估算，样本是单一产品、单一版本。

关联：[[AI-Agent-2026格局]]、[[2026-11-16-AI-Agent-harness决定token成本模型只决定天花板]]
