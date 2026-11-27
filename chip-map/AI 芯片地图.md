# AI 芯片地图

首轮公开来源扫描：**89 条规格记录**，其中 **69 条按各自公开计量范围满足双门槛**（含已公布而渠道未核实的产品），2 条特殊 SRAM 架构，18 条待核或低于门槛。部分同参数 SKU 合并展示。首轮不是全球穷举。

核对日期：2026-11-27。覆盖 Mac 和 AI PC、边缘模组、离散 GPU、国产与国际加速卡、云芯片和路线图。

- 门槛为 **AI 计算 ≥16 TOPS、外部或统一内存峰值带宽 ≥100 GB/s**。对只公布浮点矩阵指标的芯片，同时纳入 ≥16 TFLOPS（同为每秒万亿次运算的数量级，不等于 INT8 TOPS）。原样保留精度、稀疏与量化口径；不能用 FP4 稀疏的数字和 INT8 稠密直接排名。
- 带宽是十进制 GB/s。明确区分显存、片上 SRAM、DMA、PCIe、芯片互联和机架聚合。容量保留厂商 GB 或 GiB。带宽和算力必须对应同一 SKU 与范围；整卡多芯片合计可用于板卡对照，不视为单芯片共享内存。
- “已发布”表示有可识别产品与公开规格，不表示当前库存、报价或地区可买；“官方披露”只表明公开规格；“云端”不等于可购卡。所有表格数字是公开峰值或标记的推导，非板测。Apple Neural Engine TOPS 不能代替 MLX/Metal 路径实测。
- 不明数据留空待核。独立微测仅作交叉验证，见 [[芯片架构与实测数据源]]；推理与费用另见 [[大模型推理性能与成本基线]]。

## Mac、AI PC 与边缘

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| Apple M1 Ultra | 已发布 | SoC；双 die 统一内存 | 128 GB UMA | 800 | 22 TOPS Neural Engine；精度未列 | [Apple M1 Ultra](https://www.apple.com/newsroom/2022/03/apple-unveils-m1-ultra-the-worlds-most-powerful-chip-for-a-personal-computer/) |
| Apple M2 Ultra | 已发布 | SoC；双 die 统一内存 | 192 GB UMA | 800 | 31.6 TOPS Neural Engine；精度未列 | [Apple M2 Ultra](https://www.apple.com/newsroom/2023/06/apple-introduces-m2-ultra/) |
| Apple M4（Mac mini） | 已发布 | SoC | 32 GB UMA | 120 | 38 TOPS Neural Engine；精度未列 | [Apple M4](https://www.apple.com/newsroom/2024/05/apple-introduces-m4-chip/) · [Mac mini M4 规格](https://support.apple.com/en-us/121555) |
| AMD Ryzen AI Max+ 395 | 已发布 | SoC | 128 GB LPDDR5X UMA | 256 | 50 TOPS NPU | [AMD Ryzen AI Halo 配置](https://developer.amd.com/playbooks/user-guide/) · [AMD Ryzen AI Max+ 395](https://www.amd.com/en/products/processors/laptop/ryzen/ai-300-series/amd-ryzen-ai-max-plus-395.html) |
| Qualcomm Snapdragon X Elite（X1E-84/80/78） | 已发布 | SoC；按 OEM 配置 | LPDDR5X UMA | 135 | 45 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X Plus（X1P-66/64/46/42） | 已发布 | SoC；按 OEM 配置 | LPDDR5X UMA | 135 | 45 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Elite Extreme（X2E-96/94） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 228 | 80 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Elite（X2E-90/84） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 152 | 85 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Elite（X2E-88/80/78） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 152 | 80 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Plus（X2P-64/42） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 152 | 80 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| NVIDIA GB10（DGX Spark） | 已发布 | SoC 封装；UMA 非节点互联 | 128 GB LPDDR5X | 273 | 1000 TFLOPS FP4 稀疏 | [NVIDIA DGX Spark](https://www.nvidia.com/en-us/products/workstations/dgx-spark/) |
| NVIDIA Jetson Thor T5000 | 已发布 | 单模组 | 128 GB LPDDR5X | 273 | 1035 TFLOPS FP4 稠密；2070 稀疏 | [NVIDIA Thor 规格](https://developer.nvidia.com/blog/introducing-nvidia-jetson-thor-the-ultimate-platform-for-physical-ai/) |
| NVIDIA Jetson Thor T4000 | 已发布 | 单模组 | 64 GB LPDDR5X | 273 | 600 TFLOPS FP4 稠密；1200 稀疏 | [NVIDIA Thor 规格](https://developer.nvidia.com/blog/introducing-nvidia-jetson-thor-the-ultimate-platform-for-physical-ai/) |
| NVIDIA Jetson AGX Orin 64GB | 已发布 | 单模组 | 64 GB LPDDR5 | 204.8 | 275 AI TOPS（官方档位） | [NVIDIA Jetson 模块对照](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/) |
| NVIDIA Jetson Orin Nano Super 8GB | 已发布 | 开发套件，Super 档 | 8 GB LPDDR5 | 102 | 67 AI TOPS（官方档位） | [NVIDIA Jetson 模块对照](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/) |
| AMD Ryzen AI Max+ PRO 495 | 官方披露 | SoC；AMD Halo 192 GB 型 | 192 GB LPDDR5X UMA | 273 | 55 TOPS NPU | [AMD Ryzen AI Halo 配置](https://developer.amd.com/playbooks/user-guide/) · [AMD Ryzen AI Max+ PRO 495 规格](https://www.amd.com/en/products/processors/laptop/ryzen-pro/ai-max-pro-400-series/amd-ryzen-ai-max-plus-pro-495.html) · [AMD Ryzen AI Max PRO 400 发布](https://www.amd.com/en/blogs/2026/amd-powers-next-generation-agent-computers-with-new-ryzen-ai-hal.html) |

## 桌面 GPU 与工作站

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| NVIDIA GeForce RTX 5090 | 已发布 | 单卡 | 32 GB GDDR7 | 1792 | 3352 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA GeForce RTX 5080 | 已发布 | 单卡 | 16 GB GDDR7 | 960 | 1801 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA GeForce RTX 5070 Ti | 已发布 | 单卡 | 16 GB GDDR7 | 896 | 1406 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA GeForce RTX 5070 | 已发布 | 单卡 | 12 GB GDDR7 | 672 | 988 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA GeForce RTX 5060 Ti | 已发布 | 单卡 | 8 / 16 GB GDDR7 | 448 | 759 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA GeForce RTX 5060 | 已发布 | 单卡 | 8 GB GDDR7 | 448 | 614 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA GeForce RTX 5050 | 已发布 | 单卡 | 8 GB GDDR6 | 320 | 421 AI TOPS（厂商口径） | [NVIDIA GeForce 官方规格](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/) |
| NVIDIA RTX PRO 6000 Blackwell Workstation | 已发布 | 单卡；勿与 Max-Q 混用 | 96 GB GDDR7 ECC | 1792 | 4000 AI TOPS（厂商口径） | [NVIDIA RTX PRO 产品页](https://www.nvidia.com/en-us/products/workstations/professional-desktop-gpus/rtx-pro-6000/) |
| AMD Radeon RX 9070 XT | 已发布 | 单卡 | 16 GB GDDR6 | 640 | 1557 TOPS INT4 稀疏 | [AMD RX 9070 XT](https://www.amd.com/en/products/graphics/desktops/radeon/9000-series/amd-radeon-rx-9070XT.html) |
| AMD Radeon AI PRO R9700 | 已发布 | 单卡 | 32 GB GDDR6 | 640 | 383 TOPS INT8 稠密；1531 TOPS INT4 稀疏 | [AMD Radeon AI PRO R9700](https://www.amd.com/en/products/graphics/workstations/radeon-ai-pro/ai-9000-series/amd-radeon-ai-pro-r9700.html) |
| Intel Arc B580 | 已发布 | 单卡 | 12 GB GDDR6 | 456 | 233 TOPS INT8 稠密 | [Intel Arc B580/B570 规格](https://download.intel.com/newsroom/2024/client-computing/Intel-Arc-B580-B570-Media-Deck.pdf) |
| Intel Arc B570 | 已发布 | 单卡 | 10 GB GDDR6 | 380 | 203 TOPS INT8 稠密 | [Intel Arc B580/B570 规格](https://download.intel.com/newsroom/2024/client-computing/Intel-Arc-B580-B570-Media-Deck.pdf) |
| Intel Arc Pro B60 | 已发布 | 单卡 | 24 GB GDDR6 | 456 | 197 TOPS INT8 | [Intel Arc Pro B60 ARK](https://www.intel.com/content/www/us/en/products/sku/243916/intel-arc-pro-b60-graphics/specifications.html) |

## 数据中心 GPU 与云端 ASIC

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| NVIDIA A100 80GB SXM | 已发布 | 单 GPU 模组；非 PCIe 版 | 80 GB HBM2e | 2039 | 624 TOPS INT8 稠密 | [NVIDIA A100](https://www.nvidia.com/en-us/data-center/a100/) |
| NVIDIA A40 | 已发布 | 单卡 | 48 GB GDDR6 | 696 | 299.3 TOPS INT8 稠密 | [NVIDIA A40 规格书](https://images.nvidia.com/content/Solutions/data-center/a40/nvidia-a40-datasheet.pdf) |
| NVIDIA L4 | 已发布 | 单卡 | 24 GB GDDR6 | 300 | 485 TOPS INT8 稀疏 | [NVIDIA L4](https://www.nvidia.com/en-us/data-center/l4/) · [NVIDIA L4 架构白皮书](https://images.nvidia.com/aem-dam/Solutions/Data-Center/l4/nvidia-ada-gpu-architecture-whitepaper-V2.02.pdf) |
| NVIDIA L40S | 已发布 | 单卡 | 48 GB GDDR6 | 864 | 733 TOPS INT4 稠密 | [NVIDIA L40S](https://www.nvidia.com/en-gb/data-center/l40s/) |
| NVIDIA H100 SXM | 已发布 | 单 GPU 模组 | 80 GB HBM3 | 3350 | 3958 TFLOPS FP8 稀疏 | [NVIDIA H100](https://www.nvidia.com/en-us/data-center/h100/) · [NVIDIA HGX 组件规格](https://docs.nvidia.com/enterprise-reference-architectures/hgx-ai-factory/latest/components.html) |
| NVIDIA H200 SXM | 已发布 | 单 GPU 模组 | 141 GB HBM3e | 4800 | 3958 TFLOPS FP8 稀疏 | [NVIDIA H200](https://www.nvidia.com/en-us/data-center/h200/) · [NVIDIA HGX 组件规格](https://docs.nvidia.com/enterprise-reference-architectures/hgx-ai-factory/latest/components.html) |
| NVIDIA B200（HGX 口径） | 已发布 | 单 GPU 模组；不取整机总和 | 180 GB HBM3e | 8000 | 9000 TFLOPS FP4 稠密（HGX 72 PFLOPS ÷ 8） | [NVIDIA HGX 平台](https://www.nvidia.com/en-us/data-center/hgx/) · [NVIDIA HGX 组件规格](https://docs.nvidia.com/enterprise-reference-architectures/hgx-ai-factory/latest/components.html) |
| NVIDIA B300（HGX 口径） | 已发布 | 单 GPU 模组；不取整机总和 | 288 GB HBM3e | 8000 | 18000 TFLOPS FP4 稀疏（HGX 144 PFLOPS ÷ 8）；官网另一档次分列 | [NVIDIA HGX 平台](https://www.nvidia.com/en-us/data-center/hgx/) · [NVIDIA HGX 组件规格](https://docs.nvidia.com/enterprise-reference-architectures/hgx-ai-factory/latest/components.html) |
| NVIDIA Rubin（HGX） | 官方披露 | 单 GPU；非 NVL72 机架 | 288 GB HBM4 | 22000 | 35000 TFLOPS NVFP4 稠密训练；50000 稀疏推理 | [NVIDIA HGX 平台](https://www.nvidia.com/en-us/data-center/hgx/) |
| AMD Instinct MI300X | 已发布 | 单 GPU 模组 | 192 GB HBM3 | 5300 | 2610 TFLOPS FP8 稠密 | [AMD ROCm Instinct 规格对照](https://rocm.docs.amd.com/projects/ai-ecosystem/en/latest/optimization/workload-optimization.html) |
| AMD Instinct MI325X | 已发布 | 单 GPU 模组 | 256 GB HBM3e | 6000 | 2610 TFLOPS FP8 稠密 | [AMD ROCm Instinct 规格对照](https://rocm.docs.amd.com/projects/ai-ecosystem/en/latest/optimization/workload-optimization.html) |
| AMD Instinct MI350X | 已发布 | 单 GPU 模组 | 288 GB HBM3e | 8000 | 约 5000 TFLOPS FP8 稠密 | [AMD ROCm Instinct 规格对照](https://rocm.docs.amd.com/projects/ai-ecosystem/en/latest/optimization/workload-optimization.html) |
| AMD Instinct MI355X | 已发布 | 单 GPU 模组 | 288 GB HBM3e | 8000 | 约 5000 TFLOPS FP8 稠密 | [AMD ROCm Instinct 规格对照](https://rocm.docs.amd.com/projects/ai-ecosystem/en/latest/optimization/workload-optimization.html) |
| AMD Instinct MI455X | 官方披露 | 单 GPU；不推断渠道可得性 | 432 GB HBM4 | 23300 | 20100 TFLOPS（厂商峰值，精度详见规格） | [AMD MI455X](https://www.amd.com/en/products/accelerators/instinct/mi400/mi455x.html) · [AMD MI400 披露](https://newsroom.amd.com/news/amd-advances-instinct-mi400-series-ai-scientific-computing/) |
| Intel Gaudi 2 | 已发布 | 单加速器 | 96 GB HBM | 2460 | 865 TFLOPS FP8 MME（白皮书口径） | [Intel Gaudi 2/3 白皮书](https://cdrdv2-public.intel.com/817486/gaudi-3-ai-accelerator-white-paper.pdf) |
| Intel Gaudi 3 | 已发布 | 单加速器 | 128 GB HBM | 3700 | 1678 TFLOPS FP8 MME（该白皮书口径） | [Intel Gaudi 2/3 白皮书](https://cdrdv2-public.intel.com/817486/gaudi-3-ai-accelerator-white-paper.pdf) |
| Google TPU v5p | 云端 | 单芯片，物理容量见源 | 95 GiB（计算实例规格） | 2765 | 459 TFLOPS BF16 | [Google TPU 云端规格](https://docs.cloud.google.com/docs/compute-area/tpus?hl=en) |
| Google TPU v6e（Trillium） | 云端 | 单芯片 | 32 GiB HBM | 1638 | 918 TFLOPS BF16 | [Google TPU 云端规格](https://docs.cloud.google.com/docs/compute-area/tpus?hl=en) |
| Google TPU7x（Ironwood） | 云端 | 单芯片 | 192 GiB HBM | 7380 | 4614 TFLOPS FP8 | [Google TPU 云端规格](https://docs.cloud.google.com/docs/compute-area/tpus?hl=en) |
| Google TPU 8t | 官方披露 | 单芯片；训练导向 | 216 GB HBM | 6528 | 12600 TFLOPS FP4 | [Google TPU 8t/8i 技术披露](https://cloud.google.com/blog/products/compute/tpu-8t-and-tpu-8i-technical-deep-dive) |
| Google TPU 8i | 官方披露 | 单芯片；推理导向 | 288 GB HBM | 8601 | 10100 TFLOPS FP4 | [Google TPU 8t/8i 技术披露](https://cloud.google.com/blog/products/compute/tpu-8t-and-tpu-8i-technical-deep-dive) |
| AWS Trainium | 云端 | 单芯片；原文 820 GiB/s 换算 | 32 GiB HBM | 约 880.5 | 380 TOPS INT8 | [AWS Neuron Trainium](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/about-neuron/arch/neuron-hardware/trainium.html) |
| AWS Trainium2 | 云端 | 单芯片；HBM 非 DMA 规格 | 96 GiB HBM | 2900 | 1299 TFLOPS FP8（版本表口径） | [AWS Neuron Trainium3（含 Trn2 对照）](https://awsdocs-neuron.readthedocs-hosted.com/en/v2.33.1/about-neuron/arch/neuron-hardware/trainium3.html) |
| AWS Trainium3 | 云端 | 单芯片；HBM 非 NeuronLink | 144 GiB HBM | 4900 | 2517 TFLOPS MXFP8/MXFP4（版本表口径） | [AWS Neuron Trainium3（含 Trn2 对照）](https://awsdocs-neuron.readthedocs-hosted.com/en/v2.33.1/about-neuron/arch/neuron-hardware/trainium3.html) |
| Microsoft Maia 200 | 云端部署 | 单芯片；厂商称已部署，区域可用性分查 | 216 GB HBM3e | 7000 | 超过 5000 TFLOPS FP8 | [微软 Maia 200](https://blogs.microsoft.com/blog/2026/01/26/maia-200-the-ai-accelerator-built-for-inference/) |
| AWS Inferentia2 | 云端 | 单芯片；820 GiB/s 换算 | 32 GiB HBM | 约 880.5 | 380 TOPS INT8 | [AWS Neuron Inferentia2](https://awsdocs-neuron.readthedocs-hosted.com/en/v2.25.0/general/arch/neuron-hardware/inferentia2.html) |

## 中国及其他独立加速器

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| 华为 Atlas 300I Duo | 已发布 | 双芯片板卡合计，不是统一池 | 48 / 96 GB LPDDR4X | 408 | 280 TOPS INT8（整卡） | [华为 Atlas 300I Duo](https://e.huawei.com/cn/products/computing/ascend/atlas-300i-duo) |
| 华为 Atlas 300I A2 | 已发布 | 单卡产品规格 | 32 GB ECC | 800 | 560 TOPS INT8；280 TFLOPS FP16 | [华为 Atlas 300I A2](https://e.huawei.com/cn/products/computing/ascend/atlas-300i-a2) |
| 寒武纪 MLU370-X4 | 已发布 | 单卡 | 24 GB LPDDR5 | 307.2 | 256 TOPS INT8 | [寒武纪 MLU370-X4](https://cambricon.com/index.php?a=lists&c=index&catid=371&m=content) |
| 摩尔线程 MTT S4000 | 已发布 | 单卡 | 48 GB GDDR6 | 768 | 200 TOPS INT8（规格书） | [摩尔线程 S4000](https://docs.mthreads.com/s4000/s4000-doc-online/product_specifications/) |
| 摩尔线程 MTT S5000 | 厂商称已商用 | 单卡；卡间互联另计 | 80 GB 显存 | 1600 | 1000 TFLOPS AI 稠密（年报表述） | [摩尔线程年报](https://mt-website-prod.mthreads.com/uploaded/investor/%E6%91%A9%E5%B0%94%E7%BA%BF%E7%A8%8B2025%E5%B9%B4%E5%B9%B4%E5%BA%A6%E6%8A%A5%E5%91%8A%E6%91%98%E8%A6%81.pdf) |
| 昆仑芯 R200 / R200-8F | 已发布 | 单卡；两 SKU 容量不同 | 16 / 32 GB GDDR6 | 512 | 256 TOPS INT8；128 TFLOPS FP16 | [昆仑芯 R200](https://www.kunlunxin.com/product/274.html) |
| Furiosa RNGD | 已发布 | 单 PCIe 卡 | 48 GB HBM3 | 1500 | 512 TOPS INT8；512 TFLOPS FP8 | [Furiosa RNGD 规格](https://developer.furiosa.ai/v2026.1.0-rc2/en/overview/rngd.html) |
| Rebellions Rebel100／REBEL-Quad | 官方披露 | 多 chiplet 加速卡；整卡规格 | 144 GB HBM3e | 4800 | 早期简报 2048 TFLOPS FP8／卡；与 [服务器页](https://rebellions.ai/rebellions-product/rebelserver/)口径有冲突，暂不作归一化比较 | [Rebellions REBEL-Quad](https://rebellions.ai/wp-content/uploads/2025/08/RebellionsREBEL-Quad_brochure_v1.1.pdf) |
| Tenstorrent Blackhole p100a | 已发布 | 单卡 | 28 GB GDDR6 | 448 | 664 TFLOPS BLOCKFP8 | [Tenstorrent Blackhole](https://docs.tenstorrent.com/aibs/blackhole/index.html) |
| Tenstorrent Blackhole p150a/b | 已发布 | 单卡；a/b 区分接口配置 | 32 GB GDDR6 | 512 | 664 TFLOPS BLOCKFP8 | [Tenstorrent Blackhole](https://docs.tenstorrent.com/aibs/blackhole/index.html) |
| Qualcomm Cloud AI 100 Ultra | 已发布 | 4 SoC 整卡合计；每 SoC 内存分域 | 128 GB LPDDR4X | 548 | 870 TOPS INT8（整卡） | [Qualcomm AI100 架构表](https://qualcomm.github.io/cloud-ai-sdk-pages/1.21/Getting-Started/Architecture/index.html) |
| Rebellions ATOM-Max CA25 | 厂商列为可询价 | 单 PCIe 卡；最大功耗 350 W | 64 GB GDDR6 | 1024 | 512 TOPS INT8；1024 TOPS INT4 | [厂商规格](https://rebellions.ai/rebellions-product/atom-max/) |
| Rebellions CA22（ATOM / ATOM+） | 已发布 | 单 PCIe 卡；区别于旧 CA02 | 16 GB GDDR6 | 256 | 128 TOPS INT8 | [厂商规格](https://rebellions.ai/rebellions-product/rbln-ca22/) · [SDK 支持](https://rebellions.ai/developers/) |
| d-Matrix Corsair | 厂商称向部分客户量产 | 单 PCIe 卡；2 GB SRAM、150 TB/s 另计 | 最高 256 GB LPDDR5 | 400 | 2400 TFLOPS MXINT8 稠密（厂商原单位） | [产品简报](https://www.d-matrix.ai/wp-content/uploads/2024/11/d-Matrix-Corsair-Product-Brief-FINAL.pdf) · [技术白皮书](https://d-matrix.ai/pdf/d-Matrix-WhitePaper-Technical-FINAL.pdf) · [生产公告](https://www.d-matrix.ai/announcements/d-matrix-corsair-ai-inference-platform-enters-full-production-to-meet-customer-demand/) |

## 特殊存储架构

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| Groq GroqChip LPU | 特殊架构 | 单芯片；片上 SRAM | 230 MB SRAM | 80000（SRAM） | 750 TOPS INT8 | [GroqChip 数据表](https://groq.com/wp-content/uploads/2024/08/GroqChip%E2%84%A2-Processor-Product-Brief-v1.7.pdf) |
| Cerebras WSE-3 | 特殊架构 | 整片晶圆；片上 SRAM | 44 GB SRAM | 21000000（SRAM） | 125 PFLOPS 峰值 AI；精度口径核对 | [Cerebras WSE-3](https://www.cerebras.ai/press-release/cerebras-announces-third-generation-wafer-scale-engine) |

## 待核或低于双门槛

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| Apple M3 / M3 Pro / M3 Max | 待核 | SoC；不拿媒体估算当官方量化峰值 | 最高 24 / 36 / 128 GB UMA | 100 / 150 / 400 | Neural Engine 数值未在对应一手规格核实 | [Mac M3 系列对照](https://www.apple.com/mac/compare/?modelList=MacBook-Air-M3-15%2CMacBook-Pro-16-M3) |
| Apple M3 Ultra | 待核 | SoC；统一内存 | 最高 512 GB UMA | 819 | 32 核 Neural Engine；该规格页未列 TOPS | [Apple Mac Studio 2025](https://support.apple.com/en-us/122211) |
| Apple M4 Pro / M4 Max | 待核 | SoC；Max 分 GPU SKU | 最高 64 / 128 GB UMA | 273 / 410–546 | 16 核 Neural Engine；不直接继承 M4 的 38 TOPS | [MacBook Pro M4 Pro/Max](https://support.apple.com/en-ie/121553) |
| Apple M5 | 待核 | SoC；统一内存 | 最高 32 GB UMA | 153 | 未核实可直接比较的绝对 AI 峰值 | [Apple M5](https://www.apple.com/newsroom/2025/10/apple-unleashes-m5-the-next-big-leap-in-ai-performance-for-apple-silicon/) |
| Apple M5 Pro / M5 Max | 待核 | SoC；Max 取最高档 | 最高 64 / 128 GB UMA | 307 / 614 | GPU Neural Accelerators；未核实绝对 AI 峰值 | [Apple M5 Pro/Max](https://www.apple.com/newsroom/2026/03/apple-debuts-m5-pro-and-m5-max-to-supercharge-the-most-demanding-pro-workflows/) · [Mac Studio M5 Max/Ultra](https://www.apple.com/mac-studio/specs/) |
| Apple M5 Ultra | 待核 | 四 die SoC；不是四台 Mac | 最高 512 GB UMA | 1200 | 32 核 Neural Engine；未核实绝对 AI 峰值 | [Mac Studio M5 Max/Ultra](https://www.apple.com/mac-studio/specs/) · [Apple M6/M5 Ultra](https://www.apple.com/newsroom/2026/08/apple-introduces-m6-and-m5-ultra-for-a-big-leap-in-performance-and-ai-compute/) |
| Apple M6 | 待核 | SoC；区分新 NE 设计和实测 | 最高容量按机型 | 待核具体 SKU | 双 16 核 Neural Engine；未核实绝对 AI 峰值 | [Apple M6/M5 Ultra](https://www.apple.com/newsroom/2026/08/apple-introduces-m6-and-m5-ultra-for-a-big-leap-in-performance-and-ai-compute/) |
| Intel Core Ultra X9 388H | 待核 | SoC；实际通道按 OEM 配置 | LPDDR5X-9600 等 | 按实际总线位宽核 | 122 TOPS INT8 GPU | [Intel 388H ARK](https://www.intel.com/content/www/us/en/products/sku/245526/intel-core-ultra-x9-processor-388h-18m-cache-up-to-5-10-ghz/specifications.html) |
| 华为昇腾 950PR / 950DT | 待核 | 区分芯片路线图、Atlas 卡和超节点 | 型号/板卡变体单独核对 | 不同公开口径待拆 | 厂商披露 FP8/FP4；不跨 SKU 填数 | [华为昇腾技术路线](https://www.huawei.com/cn/news/2025/9/hc-xu-keynote-speech) · [华为 2026 路线图更新](https://www.huawei.com/en/news/2026/9/hc-wang-keynote) |
| 华为昇腾 960 / 970 / 980 | 路线图 | 路线图；按 PR/DT 与修订拆分 | 待核 SKU | 待核 SKU | 有厂商路线图，不当实测 | [华为 2026 路线图更新](https://www.huawei.com/en/news/2026/9/hc-wang-keynote) |
| 寒武纪 MLU370-X8 | 待核 | 板卡与双芯粒范围待核 | 待复核 | 待复核 | 已发布，待核产品规格全表 | [寒武纪 MLU370-X8 首发](https://cambricon.com/index.php?a=show&c=index&catid=127&id=48&m=content) |
| 沐曦 曦云 C500 | 待核 | 单卡；MetaXLink 不是显存带宽 | 64 GB 高带宽显存 | 官方产品页未量化 | 官方产品页未量化 | [沐曦 C500](https://www.metax-tech.com/prod.html?cid=107&id=21) |
| Qualcomm AI200 | 官方披露待核 | 单卡容量；整机指标不下摊 | 768 GB LPDDR | 绝对带宽待核 | 绝对算力待核 | [Qualcomm AI200/AI250 路线图](https://www.qualcomm.com/news/releases/2025/10/qualcomm-unveils-ai200-and-ai250-redefining-rack-scale-data-center-inference-performance-for-the-ai-era) |
| Qualcomm AI250 | 路线图 | 近存计算；勿把有效倍率当物理链路 | 待核 | 厂商仅称逾 10 倍有效带宽 | 待核 | [Qualcomm AI200/AI250 路线图](https://www.qualcomm.com/news/releases/2025/10/qualcomm-unveils-ai200-and-ai250-redefining-rack-scale-data-center-inference-performance-for-the-ai-era) |
| SambaNova SN40L | 待核 | 单 RDU 多层内存；不得合并带宽 | 64 GB HBM + 768 GB DDR + 520 MB SRAM | 未核到同口径分层带宽 | 厂商整机 BF16 数据不替代单芯片 | [SambaNova SN40L 规格](https://sambanova.ai/hubfs/SambaRack%20data%20sheet%20template%2007%2001%2025%20%281%29.pdf) |
| Hailo-10H | 待核 | 边缘模组，PCIe 不能当内存带宽 | LPDDR4/4X | 未核实达到 100 GB/s | 40 TOPS INT4 | [Hailo-10H 产品页](https://hailo.ai/products/ai-accelerators/hailo-10h-ai-accelerator/) |
| 昆仑芯 M100 | 待核 | 厂商称已公开展示 | 待核 | 待核 | 待核 | [百度 2026 Q3 官方披露](https://ir.baidu.com/news-releases/news-release-details/baidu-announces-third-quarter-2026-results) |
| 沐曦 C550 / C600 | 待核 | 已见官方软件文档中的型号；不是同一 SKU | 待核 | 待核 | 待核 | [沐曦 AI 应用发布说明](https://developer.metax-tech.com/api/client/document/preview/%E5%8F%91%E5%B8%83%E8%AF%B4%E6%98%8E/AI%E5%BA%94%E7%94%A8_%E5%8F%91%E5%B8%83%E8%AF%B4%E6%98%8E/%E6%9B%A6%E4%BA%91C500%E7%B3%BB%E5%88%97/3.9.0/split_files/%E5%B7%B2%E7%9F%A5%E9%97%AE%E9%A2%98%E5%92%8C%E4%BD%BF%E7%94%A8%E9%99%90%E5%88%B6.html) |

## 下轮优先补查

1. 国产卡：昇腾 910B/C、950 各板型、昆仑芯 M100／M300、沐曦 C550/C600、寒武纪后续产品、燧原、壁仞、天数智芯、海光 DCU、芯原以外的推理加速器。优先找产品规格、开发文档、公开披露或送样公告；媒体只作为查找线索。
2. 国际路线图与新入场：NVIDIA Rubin Ultra、AMD MI450X/MI500、Intel Crescent Island、AWS Trainium 后续、Meta MTIA、Google TPU 新档位、d-Matrix、SambaNova、日韩与欧洲厂商。核对披露、样品、部署、商业可用性，不从预期时间推定量产。
3. 端侧与现货补齐：Apple 各 SoC 的可追溯 AI 峰值、Intel/AMD/OEM 的实际内存位宽及通道、GeForce/Radeon/Arc 旧款和移动档、机器人模组、独立 NPU。淘汰项用硬证据说明原因，不因没有披露而标成不达标。
4. 待补字段：板卡功耗、标准接口与规模、软件栈及端到端实测；不以规格值直接推断 LLM 吞吐或成本。
