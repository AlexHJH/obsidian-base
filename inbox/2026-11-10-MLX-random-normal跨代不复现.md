# mx.random.normal 不是跨代可复现：同一 key 在 M1 与 M3/M5 出不同结果
- 来源: https://github.com/ml-explore/mlx/pull/4810 （issue #3568，讨论 #4809）
- 日期: 2026-11-03
- 标签: #MLX #随机数 #可复现性 #确定性

## 摘要
mlx 在 `random.normal()` 的 docstring 里补了一条 note：输出只保证「同一台机器上按 key 确定」，M1 家族（`applegpu_g13`）与后续 Apple GPU 家族（M3、M5）对同一 key 产生不同结果；分歧被定位在 `erfinv` 这一步，`uniform` / `key` / `split` 不受影响。

## 要点
- 作者选方案 b：只写文档、不改行为；可移植 `erfinv`（方案 a）留作后续单独改动 → 当前 MLX 没有跨代可移植的 normals
- 受损场景是 PR 原话点出的：跨机复现、golden hash、预注册实验——失败静默，不报错
- 与 LLM-42 类问题不同源：这里是同一 seed 换机型就变，不是动态 batching 下 reduction schedule 变
- 工程规避：要求跨机一致的采样器别用 `mx.random.normal`，固定机型，或用 `uniform` + 自实现 Box–Muller
- 口径：文档级修正确认既有行为，PR 未附新硬件实测

关联：[[MLX生态2026]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
