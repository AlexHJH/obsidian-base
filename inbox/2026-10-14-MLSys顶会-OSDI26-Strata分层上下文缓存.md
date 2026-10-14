# OSDI 2026：Strata 分层上下文缓存进 SGLang，科大首获 Jay Lepreau 最佳论文
- 来源: https://www.usenix.org/conference/osdi26/presentation/xie-zhiqiang ; https://arxiv.org/abs/2508.18572 ; https://zgkdb.ustc.edu.cn/download/1777
- 日期: 2026-07-13 ~ 07-15（Seattle，录用 136 篇）
- 标签: #OSDI #系统顶会 #KV缓存 #长上下文

## 摘要
Strata（Stanford/NVIDIA/SJTU 等，Kozyrakis 组）针对长上下文服务里「KV cache 超出显存、需从主机/外部存储回搬」的瓶颈：用 GPU 辅助 I/O 解耦 GPU 与主机侧布局，消除 paged layout 导致的碎片化小 IO；再用 cache-aware 调度缓解 delay hit（同上下文并发请求撞上 miss）、用 batch 平衡掩盖加载延迟、把不可避免的停顿与互补任务重叠。

## 要点
- 已并入 SGLang 并在生产部署，长上下文基准 TTFT 比 vLLM+LMCache 低至 1/5，比 TensorRT-LLM 快 3.75×，短上下文无退化
- 关键点是「布局解耦 + 调度」而非单纯换存储介质，与 HBF/分层内存的硬件路线互补
- 同届最佳论文（Jay Lepreau Best Paper）由中国内地机构首次以第一完成单位拿下（作者全部来自国内单位，共 3 篇获奖）
- 口径：数字来自论文/作者自述，未做独立复现；arxiv 2508.18572 为 2025-08 版
- 关联 [[MLSys2026论文精选]]、[[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]
