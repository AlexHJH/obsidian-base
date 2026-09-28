# OSDI '26：接收率拉到 20%，LLM 系统论文集中在推理诊断与静默故障检测

- 来源: https://www.usenix.org/conference/osdi26/technical-sessions ; https://paper.lingyunyang.com/reading-notes/conference/osdi-2026
- 日期: 2026-07-13
- 标签: #MLSys #OSDI #系统顶会 #LLM推理

## 摘要
第 20 届 OSDI（7/13–15，西雅图）接收 136/681 ≈ 20%（CFP 明确「至少 20%」）。LLM 系统论文集中在三条线：在线推理的可观测与调度、静默故障/发散检测、异构与碎片化资源编排。

## 要点
- 推理服务与诊断：StriaTrace（SJTU + 阿里，关键路径追踪 + 回归式 roofline 诊断在线异常）、Kairox（在线神经元再平衡做 GPU–CPU 混合推理）、Revisiting Pipeline Parallelism（把 PP 重新作为 TP 的替代做在线 serving）、Prism（GPU 显存 ballooning 做多模型降本）
- 训练可靠性成独立赛道：OpGuard（UMich + ByteDance Seed，用跨 run 的 bitwise 对齐作为生产环境定位静默发散的调试原语）、TrainMover（弹性 + 备用机中断恢复，低停机且零内存开销）
- 资源编排：OpenTela（ETH/Cambridge/EPFL/MIT/HKUST，用户态编排层把碎片化 HPC 集群变成容错异构 serving 平台）、RoCE BALBOA（ETH，SmartNIC 上的服务增强 RDMA offload 引擎）
- 端侧：Sereno（移动 LLM 推理的访存带宽争抢治理）、Asymmetry-Aware Scalable DNN Inference on Mobile CPUs
- 判据：与 [[MLSys2026论文精选]] 记录的「验证与度量必须独立于提交者」同向——静默错误检测已从单篇论文长成会议主线
- 关联 [[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]]
