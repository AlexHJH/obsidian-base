# DirectKV：GH200 上零拷贝 KV cache 卸载（OSDI'26）
- 来源: https://www.usenix.org/system/files/osdi26-luo.pdf
- 日期: 2026-07-15
- 标签: #大模型推理 #KVcache #OSDI26 #零拷贝

## 摘要
UVA（Shutian Luo / Haiying Shen）提出 DirectKV，首个针对 NVLink-C2C 超片（GH200/GB200）的零拷贝 KV cache 卸载系统：GPU kernel 直接访存 pinned CPU 内存里的 KV，取消 HBM staging buffer，并把带宽压力从互连挪回 HBM。相对 Pie/FlexGen/Neo 类方案，传输量降 50%、GPU 显存降 43%、端到端最高 1.2×。

## 要点
- 朴素零拷贝很差：PCIe 上慢 20×+，C2C 上也仍慢 2×——矩阵乘 kernel 是为 HBM 常驻张量调的，反复远端取数还拉低 L2 命中率
- 解法三件套：CPU-memory-aware tiling（经 SMEM 分块，把瓶颈从 900GB/s 互连转回 4TB/s HBM）、warp 级流水重叠取数/计算/回写、KV 投影与 attention 融合让 K/V 留在 SMEM 不回写 CPU
- 附带收益：零拷贝天然适配 CUDA Graph——KV 在 kernel 内解析地址，不像 swap 方案每步 cudaMemcpyAsync 地址变化导致图重捕获
- 测试：GH200（96GB HBM3 + LPDDR5X）、Llama-3.1-8B / OPT-13B/30B、ShareGPT+Alpaca、1K–32K 上下文；对照 Pie / FlexGen / Neo / SGLang(HBM 基线)
- 冷静看待：1.2× 是相对换页方案的量级，C2C 900GB/s 与 HBM 4TB/s 的差距没消失，零拷贝只是把「双倍搬运」省掉；源码 github.com/shutianluo/DirectKV
