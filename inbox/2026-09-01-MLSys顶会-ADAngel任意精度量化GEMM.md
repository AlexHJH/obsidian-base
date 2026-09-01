# ADAngel：任意精度量化 LLM 的自适应计算映射 (OSDI'26)
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions
- 日期: 2026-09-01
- 标签: #MLSys #OSDI #大模型推理 #量化

## 摘要
上交 ADAngel 针对任意精度量化 (APQ，如 W4A8) 把 GEMM 变为混合精度 mpGEMM，但现有静态、负载不感知的 kernel 只能加速相似 shape/位宽的任务。提出 DPR（分解-部分积-重构）计算模型，系统生成多样化 mpGEMM 算法族。

## 要点
- DPR 模型按不同位划分方案生成 mpGEMM 算法组合
- 按工作负载自适应选择 kernel，构建 Computation Strategy Set
- 关联 [[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]]
