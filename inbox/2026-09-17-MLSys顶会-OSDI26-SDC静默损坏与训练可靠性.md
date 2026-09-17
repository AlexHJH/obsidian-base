# OSDI'26 训练可靠性专场：SDC 静默损坏与角色级容错
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions
- 日期: 2026-09-17
- 标签: #OSDI #可靠性 #SDC #LLM训练

## 摘要
OSDI'26 专设「Training Reliability and Silent Errors」场次，四篇从「坏卡表征—在线检测—精确定位—恢复」串成一条链路。核心结论：合成微基准漏掉 60%+ 缺陷设备，SDC 常在老化后出现且高度数据依赖，标准 ECC 抓不到逻辑级 bit flip。

## 要点
- **SDCs in the Wild**（SJTU + 字节 Seed，Operational Systems）：表征 23 块生产集群捕获的 SDC 缺陷 GPU；三发现——① 不只出现在新硬件，多因老化后出现 ② 高度数据依赖且单元特定，通过通用压力测试的卡在特定训练输入下失败 ③ ECC/温度保护失效。SDCHunter 改为「用触发失败的确切训练负载+输入数据做执行复现」，字节部署缓解 40 起 SDC 事件
- **AEGIS**（清华 + 字节）：cSensor–cVerifier 两阶段抽象，把检测解耦为轻量损坏感知与确定性验证；3.5×10⁷ GPU 小时生产部署发现 18 起真实 SDC、13 块故障卡，开销仅 0.86%
- **RobustRL**（浙大 + 合肥 SKLMEAC）：RL 后训练故障同时来自训练与推理侧，提出角色级隔离（trainer/rollout/管理角色视为独立分布式子任务），只恢复失败角色——Detect（角色感知监控避免误报）-Restart（rollout 保状态续跑，trainer 用 warm standby 快速恢复）-Reconnect（UCX 动态点对点替代静态集合通信即时同步权重）；256 GPU + Qwen3-8B-Math + 10% 故障注入下 ETTR >80%（ByteRobust 60%），端到端训练快 8.4–17.4%
- 同场 OpGuard（UMich + 字节，位对齐调试，最佳论文提名）已归档见 [[MLSys2026论文精选]]
- 关联 [[MLSys2026论文精选]] [[AI芯片推理硬件2026全景]]
