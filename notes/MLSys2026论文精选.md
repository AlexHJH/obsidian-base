# MLSys 2026 论文精选

- 整理自: [[inbox/2026-06-14-ExecuTorch-MLSys最佳工业论文]], [[inbox/2026-06-14-LEANN-MLSys最佳论文]]
- 标签: #MLSys #端侧推理 #PyTorch #向量检索 #RAG

## ExecuTorch: 最佳工业论文
PyTorch 官方端侧推理框架，统一覆盖手机/MCU/嵌入式设备。通过 XNNPACK delegate + Arm SME2 实现 3.9× 端到端加速，支持 INT8/FP16 量化和算子级剖析，已成为 PyTorch 端侧部署标准。

## LEANN: 最佳研究论文
低存储向量索引，通过即时重计算 embedding + 压缩邻近图，将索引体积缩小 50×（仅占原始数据 ~5%），保持 SOTA 精度。使端侧 RAG 在个人设备上可行，GitHub 11K stars。

## OpenTela: 去中心化 HPC 集群统一 LLM 推理 (OSDI'26)
整理自 [[inbox/2026-06-16-OpenTela-OSDI26]]
- ETH Zurich 提出 P2P 去中心化架构，将异构 HPC 集群统一为 LLM 推理资源池
- 用 gossip 协议替代中心调度器 + CRDT 状态管理，网络分区下仍维持全局资源视图
- 支持异构 GPU 混合推理，已开源（Apache 2.0）并用于 SwissAI 生产环境
- 关联 [[AI芯片推理硬件2026全景]]

## 信号
- 端侧 AI 从「能不能跑」进入「跑得好不好」阶段
- 存储效率成为端侧推理新瓶颈，动态重算思路值得关注
- 向量检索 + 端侧推理 = 隐私保护的本地 RAG 成为可能
- OSDI'26 新增 Operational Systems 赛道，[[inbox/2026-06-17-OSDI26-ML系统论文]] 同系列
