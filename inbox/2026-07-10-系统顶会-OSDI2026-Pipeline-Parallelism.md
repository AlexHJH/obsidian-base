# OSDI 2026: 重新审视流水线并行在 LLM 推理中的应用
- 来源: https://www.usenix.org/conference/osdi26/presentation/hwang
- 日期: 2026-07-10
- 标签: #OSDI2026 #LLM推理 #流水线并行 #系统顶会

## 摘要
韩国高丽大学 Soonjae Hwang & Jeongseob Ahn 发表于 OSDI 2026。论文挑战了当前 LLM 在线推理中张量并行（TP）的统治地位，证明通过更好的调度策略，流水线并行（PP）在商用多 GPU 系统上可以获得更高吞吐。

## 要点
- 在线推理场景下 PP 的流水线气泡因 prefill/decode 阶段计算不平衡而加剧
- 提出三种技术：Greedy/Predictive chunk size 动态调整 + Delay Scheduling 重平衡 decode 负载
- 结果表明合理调度下 PP 可超越 TP，降低单节点多 GPU 推理成本
