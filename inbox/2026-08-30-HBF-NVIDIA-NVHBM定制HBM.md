# NVIDIA 发布 NVHBM：把内存控制器塞进 HBM 底座
- 来源: https://blogs.nvidia.com/blog/nvlink-fusion-nvhbm-custom-high-bandwidth-memory
- 日期: 2026-08-30
- 标签: #AI推理内存 #HBM #NVHBM #NVLinkFusion

## 摘要
NVIDIA 8/26 扩展 NVLink Fusion 推出 NVHBM——将自研内存控制器集成进 HBM base die，替代传统「控制器在 XPU die」的架构，面向半定制 XPU 生态（Amazon Annapurna Labs 首个合作）。宣称较 HBM4E 带宽 +30%、功耗 -15%，主芯片可省 25% die 面积用于计算。

## 要点
- 内存控制器下移进 3D HBM 堆叠 + 定制 PHY，减少 I/O 面积；预计 2028 年 Feynman 代 GPU 率先采用
- 定位非 HBM 替代品，而是只提供给 NVLink Fusion 半定制客户的新「构建块」，加速自研芯片落地
- 与 HBF 形成互补对照：NVHBM 压榨 HBM 带宽/能效，HBF 主打容量——AI 推理内存层级持续分化
- 相关: [[HBF-NAND-AI推理内存2026]] [[AI芯片推理硬件2026全景]]
