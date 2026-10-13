# DirectKV（OSDI'26）：NVLink-C2C 上的零拷贝 KV cache offloading

- 来源: https://www.usenix.org/conference/osdi26/presentation/luo （PDF: https://www.usenix.org/system/files/osdi26-luo.pdf）
- 日期: 2026-07-13（OSDI'26，Seattle）
- 标签: #大模型推理 #KV缓存 #零拷贝 #OSDI #内存层级

## 摘要
UVA（Shutian Luo、Haiying Shen）的 DirectKV 是首个面向 GH200/GB200 这类 CPU–GPU 超级芯片的零拷贝 KV offloading 系统：GPU kernel 直接访问 CPU 内存里的 KV，取消 HBM staging buffer，CPU 内存成为 KV 容量的直接扩展。

## 要点
- 动机：swap 式卸载既占 HBM（staging buffer）又把 CPU–GPU 传输翻倍；PCIe 40–60GB/s 对 HBM 3–4TB/s 的带宽差是根因，NVLink-C2C 的 900GB/s 才有机会
- 关键测量：朴素零拷贝在 PCIe 上慢 20×+、即便 NVLink-C2C 上仍慢 2×——matmul kernel 按 HBM 常驻张量调优，反复从 CPU 取数还会压低 L2 命中率
- 解法：用 SMEM 做 tiling 重构访存 + CPU-memory-aware CUDA kernel；把 KV 生成与 attention 融合进单个 kernel，warp 级流水线重叠抓取/计算/写回
- 实测（GH200）：CPU–GPU 传输量最高 −50%、GPU 显存 −43%、端到端最高 1.2×
- 定位：与 SAC（CXL 细粒度取 KV）互补——同是「让 KV 留在 CPU/远端，按需取」，一个靠 NVLink-C2C 带宽、一个靠 CXL 的 load/store 粒度
- 口径：摘要与引言已读原文（PDF）；性能数字为论文自测，未复现
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]
