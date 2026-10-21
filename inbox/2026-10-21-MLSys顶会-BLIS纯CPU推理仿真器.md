# BLIS：不用 GPU 的 LLM 推理部署仿真器（SIGMETRICS 2027）

- 来源: https://arxiv.org/abs/2610.14255 （代码: https://github.com/inference-sim/inference-sim）
- 日期: 2026-10-21
- 标签: #MLSys #推理系统 #仿真 #容量规划 #SLO

## 摘要
IBM 等团队提出 BLIS（Blackbox Inference Simulator）：纯 CPU、确定性的离散事件仿真器，把上线前的「选卡/并行策略/调度/准入/路由」搜索从真机试错搬进笔记本。它用一次全局拟合的物理信息延迟模型（解析基函数 + 学习到的缩放与开销项）取代逐配置 profiling，再耦合成全流水线仿真。已被 ACM SIGMETRICS 2027 接收。

## 要点
- 仿真范围覆盖 admission、路由、调度、prefill/decode 执行、KV cache 管理与抢占——正是现有预测工具各自只建模片段、忽略批处理与排队动态的地方
- 36 组实验（6 模型 × 3 种 GPU）端到端中位误差 6.7%，比真实执行快约 200×；同 seed 输出逐字节一致，可复现
- Llama-2-70B 上搜索 420 种服务配置，找到满足 300ms 平均 TTFT SLO 的 4 卡方案，而对比工具要么需双倍 GPU 要么推荐不达标方案
- 目标是对 llm-d 栈级对齐（router/EPP、P/D 分离、KV offload、autoscaling），当前引擎以 vLLM 起步
- 读法：与 [[MLSys2026论文精选]] 里 CrystalLLM（少量 GPU 忠实模拟大训练）同向——系统设计的「先仿真后上机」正在成为独立工具层；对本机多板/小规模实验环境尤其值钱
