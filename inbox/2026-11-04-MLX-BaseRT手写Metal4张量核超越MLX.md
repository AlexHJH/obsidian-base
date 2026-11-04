# BaseRT：手写 Metal 4 张量核让 M5 提示处理吞吐超 MLX 3.9 倍
- 来源: https://arxiv.org/abs/2607.19438
- 日期: 2026-07-21
- 标签: #MLX #AppleSilicon #M5 #推理 #Metal

## 摘要
BaseRT 是自研的 Metal 原生推理运行时。M5 每个 GPU 核内置 Neural Accelerator（经 Metal 4 tensor API 暴露），作者手写 dense/MoE GEMM 与 flash-attention prefill 的协作张量核 kernel，只在计算瓶颈阶段把矩阵乘路由到 NAX，decode 仍走原 SIMD kernel。15 个模型配置（Qwen3/3.5/3.6、Llama 3.2、Gemma 4，<1B–35B）上，M5 Pro 提示处理吞吐最高 6.4× llama.cpp、3.9× MLX。

## 要点
- decode 侧增益小得多：最高 1.75× llama.cpp、1.33× MLX——与 decode 受内存带宽约束的预期一致
- 收益最大在 MoE：专家投影几乎全是 GEMM，tensor path 覆盖率高；dense 小模型相对优势最小
- 关键取舍是「只换 compute-bound 段」：整条路径都换 tensor core 会让 memory-bound decode 因 kernel 切换变慢
- 推论：MLX 自带 NAX kernel 尚未吃满 M5 per-core NA，第三方运行时仍有 3–4× prefill 空间
- 关联 [[MLX生态2026]]
