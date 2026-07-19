# LEANN: MLSys 2026 最佳论文 — 低存储向量索引
- 来源: https://mlsys.org/virtual/2026/oral/3786
- 日期: 2026-05
- 标签: #MLSys #向量检索 #RAG #BestPaper #向量索引

## 摘要
LEANN 获 MLSys 2026 Best Research Paper，将向量索引压缩至原始数据的 5% 以内（最高 50× 压缩），同时保持 SOTA 精度和可比延迟，目标让 RAG 在个人设备上运行。

## 要点
- 索引大小降低 50 倍，top-3 召回仍达 90%，延迟与标准索引可比
- 面向存储受限设备（笔记本、边缘设备、本地服务器）
- 作者来自 UC Berkeley Sky Lab（Ion Stoica、Matei Zaharia、Joseph Gonzalez 等）
- 核心价值：让私有数据 RAG 摆脱云端依赖，本地即可高效运行
