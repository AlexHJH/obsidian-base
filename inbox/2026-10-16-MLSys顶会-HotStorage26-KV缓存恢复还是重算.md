# HotStorage '26：KV cache「恢复还是重算」成存储侧核心议题

- 来源: https://www.hotstorage.org/2026/accepted.html ; https://www.hotstorage.org/2026/program.html ; https://dl.acm.org/doi/proceedings/10.1145/3837053
- 日期: 2026-09-28~29（第 18 届 ACM HotStorage，布拉格，与 SOSP 2026 同城）
- 标签: #系统顶会 #KV缓存 #存储 #CXL #SSD

## 摘要
HotStorage '26 录用论文集随 ACM DL 一起公开，LLM KV cache 成为存储方向最大的主题簇：Stony Brook × IBM Research 直接把「恢复还是重算」提为研究问题，Marvell 用设备侧 DMA 把 CXL 内存做成推理 staging 层，另有多篇把 GPU 直连 I/O、分离式 KV cache 一致性、状态写入放大当作新课题。

## 要点
- **LLM KV-cache: To Restore or To Recompute, That Is the Question**（Stony Brook × IBM Research）：恢复与重算产出相同的 K/V 向量，只差消耗哪种资源——恢复吃存储与互连带宽、重算吃 GPU 算力；论文按数据驻留层级与硬件特性做 I/O-aware 选路
- **CXL Memory for LLM Inference Staging via an On-Device DMA Controller**（Marvell）：让 CXL 内存承担推理 staging，DMA 由设备侧发起以绕开 host CPU 介入
- 分离与直连两簇：Disaggregated LLM KV-Cache Storage Requires Coordination-Free Consistency（Oklahoma State）；PLINK 把 I/O 发起权交给 GPU 吃满 NVMe 并行（Samsung）
- 相邻条目：Token Write Amplification in LLM-Mediated State Stores（USTC）、计算型 SSD 做无主机 checkpoint（KAIST）、设备驻留文件系统打通 CPU-GPU I/O（HKUST-GZ / CUHK-SZ）
- 读法：KV「放哪一层」之后，第二问「怎么取回」开始有独立论文化；与 [[HBF-NAND-AI推理内存2026]] 的 SALT-KV、[[推理引擎格局-vLLM-SGLang]] 的多级 KV 卸载同向
- 口径：标题/作者取自会议 accepted 与 program 页（web_extract 被拦，经搜索摘要核对）；性能数字未读原文
- 关联 [[MLSys2026论文精选]]、[[HBF-NAND-AI推理内存2026]]
