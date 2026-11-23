# vllm-mlx：把 continuous batching 搬到 MLX，多模态靠内容哈希省掉重复视觉编码
- 来源: https://arxiv.org/abs/2601.19139
- 日期: 2026-01-27
- 标签: #MLX #AppleSilicon #推理部署 #多模态 #前缀缓存

## 摘要
针对 PyTorch MPS 不原生优化、llama.cpp 只擅长文本这两个缺口，vllm-mlx 直接在 MLX 上重建 serving 层：文本侧比 llama-cpp 吞吐高 21%–87%（Qwen3-0.6B 到 Nemotron-30B），16 并发下连续批处理做到 4.3× 聚合吞吐；多模态侧用内容哈希识别同一张图，跳过重复 vision encoder。

## 要点
- 数字（M4 Max）：文本最高 525 tok/s；重复图片查询 28× 提速，21.7s → <1s；64 帧视频分析 24.7× 缓存加速；Qwen3-VL-4B 达 143 tok/s
- 关键设计是「按内容而非按格式」哈希图片——同一张图换个编码/元数据仍命中，这比按请求 ID 命中的前缀缓存更适合多模态 agent 循环调用
- 对照口径要小心：与 llama-cpp 的差距主要是框架层（MLX 从统一内存第一天起设计 vs llama.cpp 的 Metal 后端仍按「两池搬运」组织），不全是这个项目自己的功劳
- 局限：仅 Apple Silicon，模型覆盖依赖 MLX community；作者自己把单流投机解码、跨 Mac 集群、能耗画像列为未来工作
- 关联 [[MLX生态2026]]、[[推理引擎格局-vLLM-SGLang]]
