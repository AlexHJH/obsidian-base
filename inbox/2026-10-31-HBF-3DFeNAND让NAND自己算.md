# 3D FeNAND：让 NAND 自己算，A-CiM 从存储跨到计算

- 来源: https://news.skhynix.com/en/research-inside-ep2
- 日期: 2026-10-14
- 标签: #NAND #存内计算 #ACiM #FeNAND #HBF

## 摘要
SK 海力士在 IEEE 发表研究，论证 NAND 可从存储介质演进为计算平台：以铁电材料做 3D NAND（FeNAND），在其中直接做模拟存内计算（A-CiM），能效高于传统 2D 非易失存储阵列和 3D CTN NAND。

## 要点
- 动机：商用 3D NAND 用电荷陷阱（CTN）结构，密度占优但难以稳定区分模拟计算所需的多级电流；铁电极化态分级更线性
- 结论：FeNAND 更适合做 A-CiM 平台，能效优于 2D NVM 阵列与 3D CTN NAND
- 架构含义：若成立，NAND 层不只承担「容量」（HBF 的定位），还能承担矩阵乘的模拟累加 → 与 HBF 是两条互补路线
- 一句话分工：HBF 解决「搬得动」，A-CiM 解决「不用搬」
- 边界：论文级结果，无产品时间表；模拟计算的老问题（精度、温漂、写耐久、ADC/DAC 开销）未解

关联：[[HBF-NAND-AI推理内存2026]]、[[NVIDIA-Tensor-Core-TMA与AI指令体系]]
