# SGLang 的 Rust 前端：迁移难点不是快，是「行为等价」
- 来源: https://github.com/sgl-project/sglang/pull/49640、/pull/49635、/pull/50189（链 #49640→#49650→#49653→#49663→#49907→#50189）
- 日期: 2026-11-13
- 标签: #推理部署 #SGLang #Rust #KVcache #服务框架

## 摘要
SGLang 用 `SGLANG_RUST_SERVER=1` 把 Python HTTP 前端换成 Rust（`rust/sglang-server`），调度器仍是 Python；11-13 一批 PR 集中修「Rust 进程不知道该干什么、或者干得阻塞」的问题。读下来这批 PR 的价值不在性能，而在暴露了前端重写时最容易漏的一层：Python 版本里隐含在 lifespan 与线程模型中的行为，Rust 侧得逐条重写。

## 要点
- 死锁：#49640 指出 intake 线程与 tokenizer 池用两个有界通道阻塞 `send` 互相喂 → 两边队列满即互等；改成 `try_send` + `QueueFull`（对外 503「to_scheduler channel full」），与多模态池已有的失败语义对齐
- 顺带把埋点成本修掉：`ExtensionCounter::inc` 每次观测都建一个排好序的标签向量并抢 family 级 `Mutex` → 改 `RwLock` + 原地比较，只有新标签集才升级写锁并分配；`Server.shutdown()` 也不在该处持 GIL
- tokio 侧的典型病：#49635 里 `GET /v1/loads` 在 worker 上等共享 `flock`（写者持锁期间），一停就是一个 worker，全 worker 停住则整个 listener 不响应 → 挪 `spawn_blocking`，并新增「锁被占时同 worker 上的 ticker 延迟不超过锁持有时间一半」的回归测试
- 行为等价是真成本：#50189 补齐 `--warmups` 与 `--gc-warning-threshold-secs`，因为这两者原本活在被 Rust 模式跳过的 Python HTTP 进程里；`/health` 在 warmup 完成前回 503，未标记的 warmup 在启动期直接拒绝（Python 侧只是警告）
- 错误面要逐字对齐：`/tokenize` 的 503 要落进扁平错误体的 `"type": "503"`，而不是其他 core 失败的 500
- 判读：这是「重写一个已被生产验证的组件」的标准姿势——先承认 Python 侧是规格，再一条条把隐式契约写成显式测试；和 [[#SGLang 把 KV 池收敛成一套：删 ChunkCache、draft KV 融进 target 页框（2026-11-09 归档）]] 同属「把隐式行为固定下来」这条线
- 口径：仅读 PR 描述与 11-13 合并记录，无独立基准；Rust 模式仍是 opt-in，未成为默认

关联：[[推理引擎格局-vLLM-SGLang]]、[[MLSys2026论文精选]]、[[AI-Agent-2026格局]]
