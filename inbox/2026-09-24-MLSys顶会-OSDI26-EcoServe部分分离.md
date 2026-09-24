# EcoServe（OSDI'26）：普通以太网集群靠「时间维 PD 分离」抢回 goodput
- 来源: https://www.usenix.org/conference/osdi26/presentation/du
- 日期: 2026-07-13（OSDI'26，西雅图）
- 标签: #LLM推理 #PD分离 #集群调度 #OSDI #中山大学

## 摘要
中山大学杜江溯团队的 EcoServe 面向没有 NVLink/InfiniBand 的「普通互连」GPU 集群，提出部分分离策略 PaDG：不在实例间搬 KV cache，而是在单个实例内按时间维度把 prefill 与 decode 分开执行，再用「宏实例」把多个实例循环错峰激活，保证任一时刻都有实例能接新请求。

## 要点
- PaDG 三机制：时间维 PD 分离（长窗口交替、缓阶段间干扰、省掉跨实例 KV 传输）+ 滚动激活（多实例循环进入 prefill 窗口，弥补 decode 窗口不接新请求的空档）+ 自适应路由（请求送到当前适合 prefill 的实例，随后同实例内 decode）
- 收益（32-GPU L20 + 以太网）：P90 goodput 相比 vLLM/Sarathi/DistServe/MoonCake 提升 1.96×/1.99×/2.51×/2.40×；按平均口径为 2.01×/1.87×/3.43×/3.41×
- 边界条件：H100 + NVLink/IB 上收益收窄到 1.34×/1.25×/1.75×/1.24×——高速互连缓解了 FuDG 的网络瓶颈后，PaDG 的主要收益退回「减少负载不均衡与调度开销」
- 扩展性/消融：1→4 实例时 CodeLlama2-34B/Qwen2-72B 吞吐 4.96×/5.47×；滚动激活优于随机选实例，自适应路由优于固定时间间隔
- 定位：与 [[inbox/2026-09-22-MLSys顶会-Weave-MoE-megakernel动态SM调度]]（同批 OSDI'26 分离式 RL 调度）互补——分离架构的系统收益越来越取决于互连档次与调度粒度
- 关联 [[推理引擎格局-vLLM-SGLang]] [[MLSys2026论文精选]]
