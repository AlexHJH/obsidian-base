# SGLang 如何啃下 DeepSeek V4 的百万上下文：ShadowRadix 三套 KV Cache 统一管理
- 来源: https://www.infoq.cn/article/qALuq71AxiG5VLmWqSzU
- 日期: 2026-08-21
- 标签: #大模型推理 #SGLang #KVCache #混合注意力 #RadixAttention

## 摘要
DeepSeek V4 的注意力从 V3 单一 MLA 变成三种异构注意力共存：SWA（滑窗 128）、CSA（4:1 压缩 + TopK 稀疏筛选）、HCA（128:1 高压缩）。三者 KV Cache 生命周期与索引逻辑各不相同，且 CSA/HCA 的 Compressor 机制引入跨 token 依赖，打破「逐 token 独立计算」假设，让长序列推理的缓存管理彻底变复杂。SGLang 用 ShadowRadix 实现了 Day-0 支持与百万上下文推理，并在 GB300 NVL72 上用 InferenceX 公开基准给出实测。

## 要点
- ShadowRadix = RadixTree 上扩展虚拟地址表：每个 token 一个虚拟位置，除以 4 / 除以 128 映射到三张 Shadow 页表（SWA 原位置 / CSA / HCA）
- SWA 用 Tombstone 机制自动回收滑窗外过期 KV；Shadow B/C 因压缩比固定无需淘汰
- 两个 Ring Buffer 维护 Compressor 中间状态，专门解决 CSA/HCA 的跨 token 依赖正确性
- 工程启示：混合注意力模型的缓存管理已从「分页」变成「多视图地址翻译 + 依赖追踪」
- 关联 [[推理引擎格局-vLLM-SGLang]] [[大模型推理-Agentic服务KV缓存CacheScout]]
