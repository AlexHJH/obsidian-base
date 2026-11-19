# ProfInfer：不改代码、用 eBPF 把 llama.cpp 的算子在干什么扒出来
- 来源: https://arxiv.org/abs/2601.20755 ；https://proceedings.mlsys.org/paper_files/paper/2026/hash/03dbc11a22e79cd38bea53cf518c2371-Abstract-Conference.html
- 日期: 2026-01（arXiv），MLSys 2026 Industry Track（5 月 21 日 Bellevue 现场报告）
- 标签: #MLSys #推理引擎 #eBPF #Profiling #边缘推理

## 摘要
上海交大 IPADS（Zou Bohua、Haibo Chen 等）做的细粒度非侵入式 LLM 推理剖析器。今天的推理引擎几乎没有算子级可见性，连「这负载是 memory bound 还是 compute bound」都答不出来。作者用 eBPF 在运行时函数上动态挂探针，不改源码、不重编译，把 trace 变成算子/graph/timeline/硬件计数器四类视图，主打 llama.cpp 与边缘设备。

## 要点
- 开销 <4%，作者称保真度高——这是能上生产的前提条件，instrument 类工作死在这里的居多
- 观测对象覆盖三类真实行为：dense 推理、MoE routing、算子 offloading——正好是当前边缘推理最难看清楚的三块
- 价值点在于把「我要不要 offload、offload 到哪一层」从经验变成可测；对 RK3588/Jetson 这类板子上的自定义 runtime 同样适用（eBPF 挂的是函数符号）
- 局限：eBPF 只在 Linux 可用，且需要内核支持与足够权限；Python 层与 Metal/CUDA 图内的细分拿不到，只能到 runtime 函数粒度
- 与 [[MLSys2026论文精选]] 里 XProf（Google 的开放可扩展 profiling 系统）是同一问题域的两种解法：一个改栈、一个外部挂探针

关联：[[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]]
