# zartbot 文章阅读地图
## 面向矩阵架构、数据流与 AI 系统软件的分层导读
核查日期：2026 年 10 月 25 日。本文是阅读地图和研究判断，不是对作者全部观点或性能数字的背书。

## 一、先说结论
这位作者最值得读的不是单篇行业判断，而是把模型形状、芯片数据通路、编译器调度、内存语义和互联放在同一个问题里讨论。本阅读地图聚焦矩阵架构、数据流与 AI 系统软件，主线是“数据放在哪里，谁搬，何时能用，何时能回收”，其次才是矩阵指令和峰值算力。

建议分三轮：先看 Blackwell/Rubin、TMA 和软硬件交付界面；再看 Groq/Jalapeño、NCCL GIN 和 ScaleUP；最后用 sparse/linear attention、MoE 和投机解码检验这些架构判断。

没有找到作者直接、系统比较 RISC-V IME/VME/AME 的已读文章。下文向这些方案的迁移，是本文的迁移分析，不能写成作者已经作出的结论。SIMT、SIMD、SMT、XMT 也不能混用：当前已读材料强在 GPU/SIMT、张量数据流和软件调度，尚不足以建立一套完整的 SMT/XMT 对照论证。

## 二、覆盖范围与证据等级
名称 zarbot 与 zartbot 的对应关系仍需保留不确定性。公开材料高度匹配 zartbot：作者 GitHub 博客自述公众号写作，NVIDIA/nccl 官方仓库的社区贡献文也署名 zartbot。但待识别微信短链尚未读到正文，也未确认对应标题，不能用其他文章冒充该篇摘要：
https://mp.weixin.qq.com/s/qlTlTiWoKnPzneJ1VWV_jw

本轮完成两组有边界的清单：
- 作者 GitHub 博客：16 个 issue 中，排除他人发起的 RSS 请求 #15，保留 15 篇作者文章；均已读主要正文或核心论证。未逐图审计、未复现实验，也不表示核实了所有规格。
- 公开公众号历史索引：90 条逐条作了标题或独特片段检索及可用性检查。5 条读到主干或作者英文对应正文、2 条只读到部分正文、3 条只有摘要/引言、80 条仍为目录/检索片段。5 条主干中包含一篇很可能是蚂蚁存储团队的转载，以及一篇归属暂定的镜像，不能称为 5 篇已核实原创。
- 另审读作者原站专题、署名贡献文及配套代码。原站专题与中文目录存在重叠和对应关系，不能把 90 与 15 简单相加为 105 篇独立原创。

90 条是已发现的公开索引范围，不是公众号完整历史。它覆盖 Tensor-001～010 的 2024 年题名、2025 年 60 条索引和较新首页 20 条；后续分页及部分正文不可访问。目录日期不等于已核实首发日，无法确认的日期保留空缺。配套索引保留全部 90 条、作者归属、阅读状态和可用链接。

## 三、八个优先阅读入口
### 1. Blackwell 到 Rubin：计算单元之外的真正瓶颈
[Inside Nvidia GPU: Discussing Blackwell's Limitations and Predicting Rubin's Microarchitecture](https://github.com/zartbot/blog/issues/3)，2025-11-05。
[Nvidia Rubin architecture preview](https://github.com/zartbot/blog/issues/11)，2026-07-22。

读操作数与累加器从 RF、SMEM 到 TMEM 的位置变化，异步发射、barrier、proxy fence，以及 epilogue/SFU 与稀疏重排成本。最值得带入芯片评审的问题是：矩阵阵列变大以后，尾块、非线性、描述符更新和依赖调度能否跟上？

作者的 Rubin 调度核、SM 组织等含预测，不应列为官方规格。#11 的“160 到 224 个 SM 增加 50%”是算术笔误，实际 40%，后文 1.4 倍才一致。

### 2. TMA 与 Host Memory：先把完成语义拆开
[TMA with Host memory](https://github.com/zartbot/blog/issues/2)，2025-08-04。

代码展示 mapped host memory、tensor map、异步搬运、barrier 和回写的配合。适合作为测试种子，未运行，不能据此断言任意主机映射或互联上都高效。

对矩阵扩展应分别定义：发射完成、数据传输完成、计算完成、其他执行域可见、缓冲可重用。指令名相似并不代表这些事件相同。

### 3. 软硬件交付界面：不是 SIMT 与 DSA 二选一
[同构视角下的异构计算：谈谈AI的软硬件交付界面，GPGPU or DSA](https://www.cnblogs.com/wujianming-110117/p/18993605)，目录日期 2025-07-19，公开转载六节文字已读。

文章将争论转向 processor、memory、interconnect、software 之间的 contract：SIMT 保留不规则访问与后处理弹性，张量路径要求更明确的数据驻留和协同。对 IME/VME/AME，重点应比较寄存器耦合、异步任务语义、向量后处理和跨域搬运，而不只是矩阵乘的表达方式。

转载通过同名原文引用支持归属，未与微信逐字比对。作者对部分加速器明确承认未使用、属推测，不宜据此给厂商下定论。

### 4. Groq 与 Jalapeño：确定性放在哪里
[Inside Groq LPU Architecture and explore integration with NVIDIA GPU](https://github.com/zartbot/blog/issues/4)，2025-12-31，长文在作者评论中。
[How NVIDIA Could Integrate and Absorb Groq's Technology](https://github.com/zartbot/blog/issues/5)，2026-01-07。
[重新设计推理芯片：从 Nvidia GPU 的缺陷到 OpenAI Jalapeño](https://zartbot.github.io/blog/arch/jalapeno/index.html)，页面标注 2026 年 8 月。

共同问题是控制复杂度放在硬件还是编译器：操作数放置、时间表、通知、跨芯片同步，以及确定性计算如何连接不确定的 HBM/网络。值得寻找“局部确定性”的收益，不宜把全系统绝对确定性当唯一答案。

Jalapeño 的 64×64、15/16 阵列、512KB L1、835 TFLOPS 等包含作者倒推，不是已核实规格。可对读 [OpenAI 官方介绍](https://openai.com/index/jalapeno-first-results/)与 [Groq ISCA 论文](https://cdn.sanity.io/files/chol0sk5/production/81d0c45ed70a10f1804bbd82f2d0076af1186ff0.pdf)。

### 5. NCCL GIN：统一地址不等于统一可见性
[NCCL GIN & Symmetric Memory](https://github.com/NVIDIA/nccl/blob/master/docs/contrib/GIN/NCCL_Gin_and_Symmetric_Memory/NCCL_Gin_and_Symmetric_Memory.md)，官方仓库中的署名贡献译文，主要内存映射与同步路径已读。

本地 LSA 与远端 GIN 要分开理解；远端 signal、本地 counter、flush、acquire/release 各有职责。PGAS 不是自动硬件缓存一致性。对系统软件的价值，是将搬运和通信嵌入 kernel 时仍保留清晰的 happens-before 与资源生命周期。

### 6. ScaleUP：用缓冲与尾延迟而不是品牌判断
[Discussing ScaleUP from a chip architecture perspective: smart or dumb?](https://github.com/zartbot/blog/issues/8)，2026-02-13。
[谈谈2026年ScaleUP标准的变化](https://telegra.ph/%E8%B0%88%E8%B0%882026%E5%B9%B4ScaleUP%E6%A0%87%E5%87%86%E7%9A%84%E5%8F%98%E5%8C%96-07-05)，镜像标注 2026-07-05，作者归属暂定。

把端点与交换机面积、消息粒度、聚合、incast、抖动和计算端 buffer/tile 一起考虑。后文镜像没有作者原站级证据；其协议胜负判断只作观点。建议转化为 P50/P99/P999、反压、队头阻塞、有限缓冲与算子停顿的实验清单。

### 7. Linear vs Sparse：模型数值窗口会反向约束矩阵阵列
[Linear vs Sparse：压缩即智能](https://zartbot.github.io/blog/model_arch/linear_vs_sparse/index.html)，KDA 第二篇，已读主要技术章节；数学公式与图未逐项审计。

最有价值的分类是 KV 写入、存储、读取，以及 token 轴与 channel 轴压缩。文章特别讨论 KDA chunk=16、数值重标定和 m16n8k16 映射，提示不能只把 tile 改成 128 就期待大阵列高利用率。

对架构的启发：将 state 精度、数值稳定窗口、矩阵粒度、布局和跨块依赖共同纳入评测。其 block-local 子空间压缩属于研究提议，须测真实 HBM bytes、额外构建成本、召回质量与 TPOT；不能把形式上的 FLOPs 降低当端到端收益。

谨慎处：收缩映射不一般性等于数学不可逆；有限精度下的信息损失、逆运算数值不稳定与严格不可逆应分开。“KV 块可交换”也须固定位置、因果 mask 和版本语义。此文不是目录中早期《详细分析一下Attention, Sparse or Linear?》的同篇全文。

### 8. 从 KV 复用与投机解码回看 runtime
[DeepSeek-V4.1 Flash：把 KV Cache 压缩推到极致](https://zartbot.github.io/blog/model_arch/dsv41flash_arch/index.html)，页面标注 2026 年 9 月，已读导论、架构主干和跨层复用分析，未审计全部推导。
[详细谈谈DSpark投机解码的原理](https://telegra.ph/详细谈谈DSpark投机解码的原理-07-05-3)，署名转载片段，缺前文；DSpark 和结语已读。

前者将内容、地址、查询的刷新频率分开，解释 Full/Reindex/Reuse；对编译器是跨层数据复用、索引复用与依赖管理的研究线索。作者提出的损失函数猜测和进一步结构设想不能当已披露事实。

后者更值得看硬件感知验证预算、非平滑 batch 性能、异步调度与可变长度 token 展平。投机接受率之外，还须计算草稿成本、验证成本和并发机会成本，不能只看单用户加速倍数。文中高 SLA 下的巨大百分比来自基线已接近不可用边界，不适合作一般速度承诺。

## 四、如何把文章转成自己的架构评审
以下是根据上述材料提出的工作框架，不是作者对 RISC-V 的原话。

1. 矩阵状态：IME/VME/AME 的操作数、累加器、转换和 spill 分别落在哪里；RF 端口、bank、旁路与上下文切换成本是多少。
2. 数据流：连续搬运、gather/scatter、转置、压缩/解压、低精度转换可否组合；描述符建立与更新的时间是否超过小任务收益。
3. 同步：将 issue、arrival、visibility、reuse 分开，规定标量、向量、矩阵、DMA 和互联的内存作用域。
4. 粒度：大 GEMM、小 batch GEMV、窄 K、尾块、KDA 小窗口和 sparse indexer 分别测；不要以一个 GEMM 尺寸代表模型。
5. 编译器 contract：显式 layout、异步 token、资源所有权和 barrier 生命周期如何在 Triton/MLIR/LLVM 类 IR 中保真；本轮并未找到完整的相关 lowering 教程，不应宣称覆盖。
6. 系统：CPU 控制、copy engine、缓存维护、NUMA、调度、通信与故障恢复的开销都计入 TPOT/TTFT，不只比算力。
7. 可复现性：固定芯片、固件/编译器版本、shape、精度、batch、网络、功耗、warmup 与正确性检查；分别标记实测、分析模型、作者推测。

## 五、基础教程、代码与低优先级内容
[Tensor-001 矩阵乘法分块乘法概述](https://adg.csdn.net/696f4bc5437a6b403369eda5.html) 是明确署名 zartbot 的公开转载，文字与示例代码已读。对专家读者可快速扫过 locality 和多级 tiling，不必逐页从头学习。文章声称约 53 倍，展示的 10.7669/0.2336 约为 46.1 倍；示例也不是完整的任意边界与正确性验证基准。

作者 [tensorcore_gemm 配套代码](https://github.com/zartbot/tensorcore_gemm/blob/main/05_pipeline_gmem_to_smem.cu) 已读，展示 cp.async/WMMA 与双缓冲；不要混称为 TMA/WGMMA，也不要因此声称对应《GPU内存模型》全文已读。Tensor-002～010、011、102、103 与 CuTe/CuteDSL 的多数篇目目前只有目录，附录逐条列明。

[GFD 仓库](https://github.com/zartbot/gfd) 的 README 架构与 benchmark 已读。它用 CPU 聚合碎片数据再经 copy engine DMA，适合评估控制路径与数据路径分工。作者测试条件具体，GPU queue 在部分小尺寸并不占优；14–53 倍不可泛化。

[SM_120 微架构项目](https://zartbot.github.io/micro_arch/nvidia/sm_120/index.html) 明确提示 AI 生成、未经作者审阅。可当实验线索，不应当独立规格证据。

[3FS 客户端解读](https://developer.aliyun.com/article/1660028) 虽在账号目录中，但署名及独立引用支持蚂蚁存储团队原创，宜按转载处理。FUSE/USRBIO 的 descriptor-data 分离、共享内存、通知与批处理对 SoC runtime 有参考价值。

A 股智能体、行业估值、组织与 Agent 方法论排在技术主线之后。尤其《谈谈一个新的MoA模型架构DeepSick-4.1T》目录日期是 4 月 1 日，作者后续文章称其为愚人节玩笑，不能当真实模型发布。

## 六、按时间读作者，不拼成一套静态“规格”
[2026 年 1 月 GPU-centric 网络文](https://github.com/zartbot/blog/issues/7)与 [2026 年 10 月 10T 模型文](https://github.com/zartbot/blog/issues/16)对专家规模的展望不同，前者倾向不超过 512，后者讨论 2048–4096；工作负载假设和观点会演变。#7 的跨 die 数百周期解释，在 7 月 Rubin 预览中也出现明确修正。

[Rubin + Groq 3](https://github.com/zartbot/blog/issues/9) 的 AFD 性能例子采用模拟与假设，非端到端实测；还应区分本地 SRAM 带宽与后备 DDR 加载专家的代价。

文章中的第一人称工程经验和 NetDAM/CIPU 主张很有启发，但供应商比较、路线预测及未披露实验应保留其出处和立场。最好的用法是获得可验证的问题，而不是转述成权威结论。

## 附录 A：作者 GitHub 的 15 篇文章

状态 A 表示主要正文或核心论证已读，不代表逐图审计、代码执行或独立验证。日期为 GitHub 发布日，不一定是中文首发日。

| 日期 | 编号与标题 | 重点 | 状态 |
|---|---|---|---|
| 2025-02-10 | [#1 Discuss the technological evolution related to DeepSeek MoE](https://github.com/zartbot/blog/issues/1) | 专家粒度、负载均衡、all-to-all、推理系统 | A |
| 2025-08-04 | [#2 TMA with Host memory](https://github.com/zartbot/blog/issues/2) | 数据搬运、tensor map、barrier | A |
| 2025-11-05 | [#3 Inside Nvidia GPU: Discussing Blackwell's Limitations and Predicting Rubin's Microarchitecture](https://github.com/zartbot/blog/issues/3) | RF/SMEM/TMEM、异步、CuTe、控制核 | A |
| 2025-12-31 | [#4 Inside Groq LPU Architecture and explore integration with NVIDIA GPU](https://github.com/zartbot/blog/issues/4) | 确定性、编译器、流数据通路 | A |
| 2026-01-07 | [#5 How NVIDIA Could Integrate and Absorb Groq's Technology](https://github.com/zartbot/blog/issues/5) | 数据与控制路径、融合设想 | A |
| 2026-01-10 | [#6 Challenges and Research Directions for Large Language Model Inference Hardware](https://github.com/zartbot/blog/issues/6) | 内存墙、PNM、HBF、互联 | A |
| 2026-01-28 | [#7 The Evolution of Future AI Infra and GPU centric AI Network](https://github.com/zartbot/blog/issues/7) | 计算/存储/网络协同，GPU-centric | A |
| 2026-02-13 | [#8 Discussing ScaleUP from a chip architecture perspective: smart or dumb?](https://github.com/zartbot/blog/issues/8) | 端点/交换机 trade-off、抖动 | A |
| 2026-03-18 | [#9 Inside Rubin + Groq 3 LPU architecture](https://github.com/zartbot/blog/issues/9) | Attention/FFN 分离、异构推理 | A |
| 2026-04-04 | [#10 Discussing Architectures and Interconnects in the Agentic Era](https://github.com/zartbot/blog/issues/10) | CPU、GPU、存储与总线语义 | A |
| 2026-07-22 | [#11 Nvidia Rubin architecture preview](https://github.com/zartbot/blog/issues/11) | TMA、依赖调度、稀疏 attention | A |
| 2026-07-31 | [#12 RSI](https://github.com/zartbot/blog/issues/12) | 自改进的评价与治理边界，低优先级 | A |
| 2026-08-23 | [#13 A memo before Hotchips 2026](https://github.com/zartbot/blog/issues/13) | KV 分层、HBF、模型与存储协同 | A |
| 2026-09-04 | [#14 Rethinking Loop Transformer: Loops, Recirculation, and Sparse MoE](https://github.com/zartbot/blog/issues/14) | 模型递归、专家稀疏度、KV | A |
| 2026-10-05 | [#16 On AI Infra for 10T parameters Models](https://github.com/zartbot/blog/issues/16) | 大模型需求反推通信、可靠性、安全 | A |

注：A 不等于复现了实验、逐图审计或核实所有规格；本文在梳理作者文章，不是在背书全部结论。


## 配套索引与关联阅读

- [[2026-10-25-zartbot公开文章90条索引]]：完整90条元数据、阅读状态、作者归属与可用链接。
- [[NVIDIA-Tensor-Core-TMA与AI指令体系]]：用于对照作者观点与官方ISA语义。
- [[RISC-V矩阵扩展两年进展-2024-2026]]：本文迁移分析的相关主题，不代表作者已评审这些规范。

本文只保存原创导读与元数据，不收录所引文章全文。公开转载与镜像的归属、版本和日期应分别核对；链接可访问不等于其内容已获独立验证。
