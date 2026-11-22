# Kioxia GP1 走 PCIe 6.0 高 IOPS 路线，与 HBF 在「内存扩展层」分道
- 来源: https://americas.kioxia.com/en-us/business/news/2026/ssd-20260804-1.html ；https://technologies.org/kioxia-gp1-wins-fms-best-of-show-with-10-million-iops-splitting-from-sandisk-and-sk-hynix-on-hbf
- 日期: 2026-11-22（原文 FMS 2026-08）
- 标签: #HBF #存储层级 #Kioxia #GPU直连

## 摘要
GP1 用第二代 XL-FLASH + PCIe 6.0/NVMe 2.2，512B 随机读 1000 万 IOPS、读延迟 <5μs，2026 年底向选定客户送样；定位同样是「HBM 之外的高性能扩展层」，但走板级 PCIe 而非封装内 UCIe。

## 要点
- 分工可用访问模式切开：GP1 主打随机小读低延迟，HBF 主打每 stack 512GB + 0.4–3.0TB/s 顺序带宽
- 代价不同向：HBF 要把 NAND 搬进封装、与 xPU 共同设计（SK 海力士原话：HBF 不能做成 standalone device）；GP1 保持标准 SSD 形态，软件栈是现成 NVMe
- 站队信号：Kioxia 未加入 Sandisk/SK hynix 的 HBF 联盟，等于给「不改封装」留一条路；联盟现有 Google、Meta、Tenstorrent，NVIDIA/AMD/Micron 均缺席
- 映射到推理负载：稀疏 attention 的 top-k 散射读、MoE 专家跳转更像 GP1 场景；权重流式加载、长前缀复用更像 HBF
- 口径：IOPS 与延迟为厂商标称，送样在 2026 年底，无第三方实测

关联 [[HBF-NAND-AI推理内存2026]]、[[推理引擎格局-vLLM-SGLang]]
