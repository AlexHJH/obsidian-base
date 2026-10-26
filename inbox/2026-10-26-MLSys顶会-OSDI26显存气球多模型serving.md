# Prism：用「显存气球」把多个 LLM 塞进同一张卡（OSDI'26）
- 来源: https://www.usenix.org/conference/osdi26/technical-sessions（论文 Cost-Efficient Multi-LLM Serving via GPU Memory Ballooning, osdi26-yu-shan.pdf）
- 日期: 2026-07-15
- 标签: #OSDI #LLM推理 #多模型serving #显存管理

## 摘要
面向"推理服务商要同时挂很多长尾小模型"的场景，指出生产 trace 里的模型负载呈"成组突发"（bursty-group）而非平稳分布，静态空间划分与时间片共享都无法自适应，只能在 SLO 与利用率之间二选一；Prism 用弹性显存气球让模型按需换入换出，并把请求调度转成截止期问题。

## 要点
- 关键观察：一组模型会同时变活跃、又同时退潮，空间/时间共享方案缺乏机制跟随这种漂移
- 显存气球（memory ballooning）：把不活跃模型的权重/显存让出来，而不是按模型静态切分 GPU
- 调度侧把请求转成截止期问题，用 Moore-Hodgson 算法最小化 miss 数，并用 exact time slack 排序
- 与 [[MLSys2026论文精选]] 里 KV cache 子系统化、[[推理引擎格局-vLLM-SGLang]] 的多租户方向一致：稀缺资源从"算力"移到"显存+调度"
