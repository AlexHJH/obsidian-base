# LMTracer：把探针编成 GPU 执行图里的 trace 节点，生产 8 个月平均 0.9% 开销
- 来源: https://sigops.org/s/conferences/sosp/2026/schedule.html
- 日期: 2026-11-26 (SOSP 2026；清华 + Rank Computing/趋境)
- 标签: #MLSys #SOSP #GPU剖析 #生产部署 #CUDA图

## 摘要
原有 LLM 服务剖析手段失效的根因不是采样率，而是「停下来收数据」打断了高度异步的 GPU 执行流。LMTracer 把剖析逻辑编译成轻量 probe kernel，作为特殊 trace 节点嵌进 GPU 执行图（graph-embedded probing），数据按需流式回 CPU，用户 kernel 不中断。

## 要点
- 生产数据：部署 8 个月，跨训练/推理/微调平均开销 0.9%，主动挖出 15 个性能问题，修复后服务延迟 -21.2%、吞吐 +24.5%
- 与 ProfInfer（eBPF 在运行时函数上挂探针、<4% 开销）是同一问题的两条路：一个在图内（能进 kernel 边界、只限自家图）、一个在图外（能覆盖任意 runtime、只到函数粒度）
- 判读：CUDA Graph 把 kernel 序列变成不可插桩的黑盒，剖析只能在建图时注入——这条约束对所有 graph-replay 型推理栈（含 vLLM/SGLang 的 CUDA graph 捕获路径）都成立
- 关联 [[MLSys2026论文精选]] [[推理引擎格局-vLLM-SGLang]] [[#ProfInfer：不改代码、用 eBPF 挂探针看 llama.cpp 在干什么（2026-11-22 归档）]]
