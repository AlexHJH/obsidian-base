# 知识库

> 由 Hermes Agent 维护 · Obsidian 编辑 · GitHub 同步

## 目录结构

| 目录 | 用途 | 谁写 |
|------|------|------|
| `inbox/` | 采集的原始内容 | Hermes |
| `notes/` | 整理后的永久笔记 | Hermes + 你 |
| `daily/` | 日记/随手记 | 你 |
| `interests/` | 关注的主题清单 | 你（Hermes 以此为搜索线索） |
| `templates/` | Obsidian 模板 | 你 |

## 工作流

1. Hermes 每天搜索 `interests/` 中列出的主题
2. 原始内容写入 `inbox/`
3. Hermes 定期整理 inbox → 提炼到 `notes/`，加双向链接和标签
4. Git 自动 commit + push
5. 你在 Windows Obsidian 拉取查看/编辑
