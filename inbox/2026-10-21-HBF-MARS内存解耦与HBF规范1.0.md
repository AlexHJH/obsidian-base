# SK 海力士 OCP 2026：MARS 机架级内存池把 TTFT 压 3–5×，HBF 定 1.0 规范、2028 初出 Cube 样品
- 来源: https://www.trendforce.com/news/2026/10/13/ocp-in-focus-sk-hynix-drives-up-to-5x-faster-ttft-with-mars-hbf-expands-capacity-by-14x
- 日期: 2026-10-13
- 标签: #HBF #内存墙 #KV缓存 #内存解耦 #OCP

## 摘要
SK 海力士在 OCP Global Summit 2026 由 SVP Hoshik Kim 提出 MARS（Memory-Centric AI Rack-Scale System）：把内存从 CPU/GPU 服务器节点解耦成机架级共享池，多轮 agent 推理的 KV cache 存在池里而不挤爆显存与存储，实测 TTFT 相比「重算」提升 3–5×、尾延迟明显下降。同场更新 HBF 进度：带宽约 HBM 一半、容量约 14×，规范 0.7 已在 7 月发布、1.0 明年初，HBF Cube 样品 2028 年初。

## 要点
- MARS 针对的两个结构性问题：单机空闲内存无法跨节点共享；扩容必须整机购买 → 被迫 GPU 超配，TCO 上升
- 池化粒度从「tray 级」推到「rack 级」：compute 与 memory tray 各自独立扩展，per-server headroom 换成 pool-level 效率
- HBF 定位一句话：把 HBM 级带宽扩到机架级容量——「HBF 带宽约为 HBM 的一半，容量约为 HBM 的 14 倍」
- 时间线：OCP HBF spec v0.7（2026-07）→ v1.0（2027 初）→ 概念原型 → HBF Cube 样品 2028 初
- 口径：全部为 keynote 口径，无硅片实测；3–5× 是对「重算 KV」的加速比，不是对 HBM 的替代收益
- 读法：内存墙的三个解——HBF（容量层）、CXL 池化（共享层）、KV 卸载（软件层）——正在同一场 OCP 上并置
