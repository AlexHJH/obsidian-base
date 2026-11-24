# agent-framework 被投毒：装包即执行，与是否 import 无关
- 来源: https://www.aikido.dev/blog/two-malicious-agent-framework-releases-hit-pypi-in-33-minutes ；https://www.ox.security/blog/breaking-hijacked-releases-of-microsofts-official-agent-framework-steals-azure-and-ci-credentials ；https://thehackernews.com/2026/11/attackers-compromise-microsofts-agent.html
- 日期: 2026-11-17
- 标签: #AI-Agent #供应链安全 #PyPI #凭据窃取

## 摘要
11-17 20:35 UTC 起 33 分钟内，Microsoft Agent Framework 的 PyPI 元包 `agent-framework` 连发 1.26.1、1.26.2 两个恶意版本：靠 `.pth` 文件在每次 Python 启动时执行，不需要任何人 import。目标不是开发者本机，是 CI 里的 Azure OIDC token、托管身份 token 与 pipeline 凭据，数据 zlib+base64 打到 webhook.site。

## 要点
- 触发机制决定了防不住：`.pth` 在解释器启动阶段被自动执行，`pip install` 之后就生效，静态扫描「包有没有恶意 import」全部落空
- 攻击点应是 wheel 构建流水线，不是仓库源码——包名、维护者、元数据都正常，只有构建产物被动过
- 受害面 ≈ 19 万次/月的下载量，暴露窗口约 45 分钟；官方建议钉 `agent-framework==1.26.0` 并轮换凭据
- 为什么专挑 agent 框架：这类包所在的机器天然持有模型 API key 与云控制面身份，一次拿到的不是代码仓权限而是可执行动作的权限
- 对 [[LongLoop Console]] 的含义：依赖安装属于「执行」而非「准备」，必须在隔离环境里装并锁 hash（`--require-hashes`），凭据只在出口注入、不进装着依赖的那个进程
- 关联 [[AI-Agent-2026格局]]、[[2026-11-21-AI-Agent-沙箱逃逸在出口trust-handoff]]
