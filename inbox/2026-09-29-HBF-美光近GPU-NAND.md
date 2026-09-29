# 美光「近 GPU NAND」：把高耐久 NAND 放进封装内，当显存与 SSD 之间的缓冲层
- 来源: https://www.ithome.com/0/997/668.htm , https://www.trendforce.cn/industry-news/semiconductors/20260904-7768.html , http://www.chinaaet.com/article/3000180188
- 日期: 2026-09-02（DigiTimes 首发；9/3–9/4 中文媒体转述）
- 标签: #HBF #NAND #近存计算 #显存 #AI推理内存

## 摘要
美光正探索「near-GPU NAND」：把数百 GB 级高耐久 NAND 模块直接放到 GPU 封装内部或 PCB 上（工作方式类似 HBM/GDDR7），牺牲存储密度换 I/O 速度与带宽，充当显存与远端存储池之间的高速缓冲层。

## 要点
- 与 HBF 属同一条「NAND 进封装」路线：HBF 走 OCP 标准化 + UCIe + 8/16 层堆叠，美光走自研高耐久 NAND 模块；共同目标都是让 LLM 推理不再被显存容量卡死
- 用途定位：面向非低延迟/高带宽敏感负载——长上下文里被稀疏访问的 KV、冷权重等，正好对上 HBF 讨论中的「大而冷」数据层
- 代价与开放问题：相比 TLC/QLC 密度更低，重心在 I/O 提速；封装内 NAND 的耐久、热与软件可见性仍是工程问题
- 观察点：这是三大存储厂在「HBM 之外的下一层」上的路线分化——SK 海力士/Sandisk 押标准化 HBF，美光同时押自研近存 NAND
- 关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]、[[推理引擎格局-vLLM-SGLang]]
