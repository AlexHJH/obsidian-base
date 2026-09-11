# OSDI'26｜Syncopate：以「通信块」为中心的细粒度计算通信重叠
- 来源: https://arxiv.org/abs/2601.20595
- 日期: 2026-09-11
- 标签: #MLSys #OSDI #Triton #计算通信重叠

## 摘要
UCSD/Meta/OpenAI/GMU 的 Syncopate 指出分布式编译器的现状：只在 stream 级别整 kernel 重叠计算与通信，导致额外 kernel 启动、kernel 边界全设备同步，并在慢 tile 拖尾时留下大量空隙。它提出 communication chunk 抽象，把通信粒度与 kernel 结构、后端机制解耦，做单 kernel 内的细粒度重叠。

## 要点
- 工作流：输入本地 Triton kernel + chunk 调度方案，源到源编译重排 tile 执行顺序以跟随通信进度，运行时负责发起传输并接后端
- chunk 计划来源三选一：从现有分布式编译器移植、用户手写、复用模板
- 收益面：免去额外 kernel 启动与边界同步，消除「最慢 tile 决定通信尾巴」的空转
- 趋势印证：分布式编译正从「流级重叠」走向「tile/chunk 级重叠」，与 Triton-distributed、Event Tensor 同一方向
- 关联: [[AI编译器2026格局]] [[MLSys2026论文精选]]
