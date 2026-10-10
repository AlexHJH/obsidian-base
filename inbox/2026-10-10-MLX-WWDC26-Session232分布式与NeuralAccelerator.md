# WWDC26 Session 232/233：本地 Agent 四层栈与 mlx.launch 实操参数

- 来源: https://developer.apple.com/videos/play/wwdc2026/232 ; https://developer.apple.com/videos/play/wwdc2026/233
- 日期: 2026-06（WWDC26；本次补录命令行/参数级细节）
- 标签: #MLX #AppleSilicon #Agent #分布式推理 #NeuralAccelerator

## 摘要
Apple 把本地 Agent 栈讲成四层：MLX（数组框架/Metal/内存）→ MLX-LM（加载/量化/微调）→ MLX-LM Server（OpenAI 兼容 HTTP）→ Agent。这次字幕里给出了可照抄的操作细节：分布式推理用 `mlx.launch --backend jaccl`，Neural Accelerator 不必改代码，MLX 自动选核。

## 要点
- Neural Accelerator 让矩阵乘法达到 M4 的 4×，配合乘/注意力专用内核 → Agent 每轮处理工具输出的 prompt 预处理时间近似缩到 1/4（会话常有数十万 token，多数不是生成的）
- 分布式命令：`mlx.launch --hostfile hosts.json --backend jaccl /remote/path/mlx_lm.server --model mlx-community/Qwen-3.5-122B-A3B-8bit`
- hostfile 由脚本生成：探测 Thunderbolt 拓扑、关闭 Thunderbolt Bridge、为每条链路配 RDMA；--auto-setup 直接执行，否则只打印命令供审查
- 接入方式：Xcode → Settings → Intelligence → Add Chat Provider 指向本地 MLX Server 端口（示例用 8080）即可让本地模型读工程、定位并修 Bug
- 关键概念：JACCL 是低延迟分布式通信后端；CLI/Python/Swift/C++ 四套 API 同一套语义
- 口径：官方演示，第三方未复核；「自动选核」意味着升级 MLX 版本可能改变内核选择
- 关联 [[MLX生态2026]]、[[AI-Agent-2026格局]]、[[推理引擎格局-vLLM-SGLang]]
