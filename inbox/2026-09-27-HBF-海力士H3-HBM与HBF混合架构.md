# H³：SK 海力士把 HBM 与 HBF 放进同一 interposer 的混合内存架构
- 来源: https://ieeexplore.ieee.org/document/11371745
- 日期: 2026-08
- 标签: #HBF #HBM #分层内存 #KVCache #共享前缀缓存

## 摘要
SK 海力士在 IEEE 论文中提出 H³：同一 interposer 上同时挂 HBM 与 HBF，并按数据「读写属性」而非单纯热度分层——只读数据（模型权重、预计算/共享 KV Cache）放 HBF，动态数据留 HBM。仿真显示同等 GPU 数量下可并发处理更多请求，吞吐/功耗最高 2.69× 于纯 HBM 系统，论文明确指向「巨型只读数据 + 共享前缀 KV Cache」这一 Agent 服务主场景。

## 要点
- 收益来自解耦：HBM 不再被容量挤占，专注带宽敏感路径；HBF 承担容量层
- 代价写清楚：HBF 访问延迟 μs 级（HBM 为 ns 级）、写寿命约 10⁵ 次、功耗最高为 HBM 的 4× → 只适合读多写少、可预取
- 容量/带宽：单 stack 可达 512GB（HBM 的 8–16×），带宽约 1.6–3.2TB/s，与 HBM3 同量级
- 与 FlashAccel 是同一路线的两种落地：[[HBF-FlashAccel-软硬件协同HBF加速器]]；均依赖软件层做 page 级调度
- 关联 [[HBF-NAND-AI推理内存2026]] [[推理引擎格局-vLLM-SGLang]]
