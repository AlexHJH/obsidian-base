# SK 海力士 AI Infra Summit 2026：「New Spectrum」把 HBF 放进三元分层
- 来源: https://news.skhynix.com/en/ai-infra-summit-2026 ; https://chippong.com/insights/sk-hynix-hbf-pim-salt-kv-ai-memory-tiering
- 日期: 2026-09-18
- 标签: #HBF #SK海力士 #KV-Cache #分层存储

## 摘要
SK 海力士在 9/15–17 圣克拉拉 AI Infra Summit 2026 以「New Spectrum」展出 HBF 结构模型、PIM/AiMX 与 SALT-KV 三条线（约 6000 人参会，较上届翻倍）。叙事重心从「某颗器件更快」转为按负载维度做内存放置：HBF 补长上下文容量、PIM 补 decode 带宽、SALT-KV 决定 KV 落在哪一层。

## 要点
- SALT-KV（Semantic-Aware Lifecycle Tiering for KV Cache）：按上下文语义切分 KV cache，综合评估「复用价值 vs 存储成本」后放到 GPU 显存 / CPU DRAM / eSSD 合适层；现场用配自研 eSSD 的服务器 + 监视器实时演示数据在层间迁移
- PIM/AiMX 定位 decode 密集的高价值负载（减少数据搬移），HBF 定位大 batch + 长上下文（用 3D NAND 密度换容量）——按 workload 分工而非互替
- HBF 侧仍只有结构模型，无公开平台支持与量产 benchmark；商业闸门依旧是「公开规范 + 平台适配 + 生产数据」
- 与 FMS 2026 的「分层存储」口径一脉相承，本次把 KV 放置从硬件规范落到了运行时可观测的软件方案
- 关联 [[HBF-NAND-AI推理内存2026]]、[[推理引擎格局-vLLM-SGLang]]
