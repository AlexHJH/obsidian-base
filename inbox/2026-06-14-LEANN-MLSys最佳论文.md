# LEANN: 低存储向量索引获 MLSys 2026 最佳论文
- 来源: https://arxiv.org/abs/2506.08276
- 日期: 2026-06-14
- 标签: #MLSys #向量检索 #RAG #端侧推理

## 摘要
LEANN 提出一种存储高效的向量索引，通过即时重计算 embedding 替代存储、压缩邻近图索引，将索引体积缩小 50×，同时保持 SOTA 精度和延迟。GitHub 已获 11K stars。

## 要点
- 索引仅占原始数据 ~5%，实现 97% 存储缩减
- 无需存储高维 embedding，搜索时动态重算
- 使端侧 RAG 在个人设备上可行
- MLSys 2026 Best Research Paper，repo: github.com/yichuan-w/LEANN
