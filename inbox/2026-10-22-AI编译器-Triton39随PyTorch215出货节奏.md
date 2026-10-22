# Triton 3.9 随 PyTorch 2.15 出货：版本绑定节奏本身就是信号
- 来源: https://github.com/triton-lang/triton/blob/main/RELEASE.md ; https://dev-discuss.pytorch.org/t/pytorch-release-2-15-key-dates/3434
- 日期: 2026-10-21（Triton 3.9 目标发布日）
- 标签: #AI编译器 #Triton #PyTorch #发布节奏

## 摘要
Triton 官方 RELEASE.md 的版本排期表显示：PyTorch 2.15 对应 Triton 3.9、目标发布日 2026-10-21；PyTorch 2.16 对应 3.9.1 或 3.10、发布决定日 11-10、目标 12-15。PyTorch 侧 key dates 为 10/5 切分支、10/10 出 RC1、10/28 GA，与 Triton DevConf（10/19）和 PyTorch Conf NA（10/20–21）同周，形成一次「会议周即发布周」的聚拢。

## 要点
- 版本号绑 PyTorch 而非日历：Triton 每两个 PyTorch minor 从 main 取一次 commit，所以「3.9 号段」不等于新特性集合，读版本意义要回到 commit 区间
- 上次同构节点：3.8.0 分支 6 月切、8-28 才发（比 Triton 3.7 的 5-07 晚 3 个多月），说明 patch/minor 的实际出货更受 PyTorch 与 vLLM nightly 回归约束
- 发布纪律延续：release 分支只收回归修复与静默算错/死锁类关键修复，feature work 不允许 cherry-pick
- 读法：编译器版本节奏 = 下游框架节奏的函数；评估某后端是否「可用」应看目标 PyTorch 版本对应的 Triton commit，而非 PyPI 最新号
- 口径：取自 RELEASE.md 排期表与 release 邮件列表摘要（web_extract 被拦，未读到 3.9 release notes 正文），新特性清单待发布公告核实
- 关联 [[AI编译器2026格局]]、[[推理引擎格局-vLLM-SGLang]]
