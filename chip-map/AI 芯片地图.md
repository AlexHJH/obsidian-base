# AI 芯片地图

本次公开来源扫描：**126 条规格记录**，其中 **92 条按各自计量范围满足双门槛**（含按披露或可追溯理论带宽入选的产品），2 条特殊 SRAM 架构，32 条待核或低于门槛。部分相同参数 SKU 合并展示，非全球穷举。

核对日期：2026-11-28。覆盖 Mac 和 AI PC、边缘模组、离散 GPU、国产与国际加速卡、云芯片和路线图。

- 门槛为 **AI 算力 ≥80 TOPS，且外部或统一内存峰值带宽 ≥128 GB/s**。矩阵算力也可按公开精度的 ≥80 TFLOPS 入选（同为每秒万亿次运算的数量级，不等于 INT8 TOPS）。原样保留精度、稀疏与量化口径；不能用 FP4 稀疏的数字和 INT8 稠密直接排名；不同 GPU、NPU 或 CPU 的峰值不相加凑门槛。
- 带宽是十进制 GB/s。明确区分显存、片上 SRAM、DMA、PCIe、芯片互联和机架聚合。容量保留厂商 GB 或 GiB。带宽和算力必须对应同一 SKU 与范围；整卡多芯片合计可用于板卡对照，不视为单芯片共享内存。
- “已发布”表示有可识别产品与公开规格，不表示当前库存、报价或地区可买；“官方披露”只表明公开规格；“云端”不等于可购卡。所有表格数字是公开峰值或标记的推导，非板测。Apple Neural Engine TOPS 不能代替 MLX/Metal 路径实测。
- 不明数据留空待核。已披露指标低于门槛的移出达标主表，保留原规格供对照；其他计算单元如有同配置的一手峰值，可另行复核。独立微测仅作交叉验证，见 [[芯片架构与实测数据源]]；推理与费用另见 [[大模型推理性能与成本基线]]。

## Mac、AI PC 与边缘

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| Qualcomm Snapdragon X2 Elite Extreme（X2E-96/94） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 228 | 80 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Elite（X2E-90/84） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 152 | 85 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Elite（X2E-88/80/78） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 152 | 80 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X2 Plus（X2P-64/42） | 官方披露 | SoC；机型可得性分查 | LPDDR5X UMA | 152 | 80 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| NVIDIA GB10（DGX Spark） | 已发布 | SoC 封装；UMA 非节点互联 | 128 GB LPDDR5X | 273 | 1000 TFLOPS FP4 稀疏 | [NVIDIA DGX Spark](https://www.nvidia.com/en-us/products/workstations/dgx-spark/) |
| NVIDIA Jetson Thor T5000 | 已发布 | 单模组 | 128 GB LPDDR5X | 273 | 1035 TFLOPS FP4 稠密；2070 稀疏 | [NVIDIA Thor 规格](https://developer.nvidia.com/blog/introducing-nvidia-jetson-thor-the-ultimate-platform-for-physical-ai/) |
| NVIDIA Jetson Thor T4000 | 已发布 | 单模组 | 64 GB LPDDR5X | 273 | 600 TFLOPS FP4 稠密；1200 稀疏 | [NVIDIA Thor 规格](https://developer.nvidia.com/blog/introducing-nvidia-jetson-thor-the-ultimate-platform-for-physical-ai/) |
| NVIDIA Jetson AGX Orin 64GB | 已发布 | 单模组 | 64 GB LPDDR5 | 204.8 | 275 AI TOPS（官方档位） | [NVIDIA Jetson 模块对照](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/) |
| Intel Core Ultra X9 388H（ThinkPad X9-15p） | 已发布 | 21VV0029TH；128-bit；理论值 | 32 GB LPDDR5X-9600 | 153.6 | 122 TOPS INT8 GPU；50 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/245526/intel-core-ultra-x9-processor-388h-18m-cache-up-to-5-10-ghz/specifications.html) · [Lenovo 21VV0029TH](https://psref.lenovo.com/Detail/ThinkPad/ThinkPad_X9_15p_Gen_1_Aura_Edition?M=21VV0029TH) |
| Intel Core Ultra X9 388H（ThinkPad P16s Gen 5） | 已发布 | 21XE006ASG；128-bit；理论值 | 96 GB LPCAMM2-8533 | 136.5 | 122 TOPS INT8 GPU；50 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/245526/intel-core-ultra-x9-processor-388h-18m-cache-up-to-5-10-ghz/specifications.html) · [Lenovo 21XE006ASG](https://psref.lenovo.com/Detail/thinkpad_p16s_gen_5_intel?M=21XE006ASG) |
| Intel Core Ultra X9 378H | 已发布 | SoC；128-bit 满宽理论值，按 OEM 实际档位 | 最高 96 GB LPDDR5X；按 OEM 配置 | 153.6 | 122 TOPS INT8 GPU；50 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/246128/intel-core-ultra-x9-processor-378h-18m-cache-up-to-5-00-ghz/specifications.html) · [Lenovo X9-15p 配置族](https://psref.lenovo.com/Product/ThinkPad/ThinkPad_X9_15p_Gen_1_Aura_Edition) |
| Intel Core Ultra X7 368H | 已发布 | SoC；128-bit 满宽理论值，按 OEM 实际档位 | 最高 96 GB LPDDR5X；按 OEM 配置 | 153.6 | 122 TOPS INT8 GPU；50 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/245523/intel-core-ultra-x7-processor-368h-18m-cache-up-to-5-00-ghz/specifications.html) · [Lenovo X9-15p 配置族](https://psref.lenovo.com/Product/ThinkPad/ThinkPad_X9_15p_Gen_1_Aura_Edition) |
| Intel Core Ultra X7 358H | 已发布 | SoC；128-bit 满宽理论值，按 OEM 实际档位 | 最高 96 GB LPDDR5X；按 OEM 配置 | 153.6 | 122 TOPS INT8 GPU；50 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/245527/intel-core-ultra-x7-processor-358h-18m-cache-up-to-4-80-ghz/specifications.html) · [Lenovo X9-15p 配置族](https://psref.lenovo.com/Product/ThinkPad/ThinkPad_X9_15p_Gen_1_Aura_Edition) |
| Intel Core Ultra 5 338H | 已发布 | SoC；128-bit 满宽理论值，按 OEM 实际档位 | 最高 96 GB LPDDR5X；按 OEM 配置 | 136.5 | 98 TOPS INT8 GPU；47 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/245531/intel-core-ultra-5-processor-338h-18m-cache-up-to-4-70-ghz/specifications.html) · [Lenovo X9-15p 配置族](https://psref.lenovo.com/Product/ThinkPad/ThinkPad_X9_15p_Gen_1_Aura_Edition) |

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
| NVIDIA GB300 Desktop Superchip（DGX Station） | Linux 有合作伙伴订购入口 | 工作站单模块；两级一致寻址；NVLink-C2C 900 GB/s 双向合计另列 | 252 GB HBM3e + 496 GB LPDDR5X（合计 748 GB） | 7100 HBM / 396 LPDDR5X；不相加 | 20000 TFLOPS FP4 稀疏 | [NVIDIA 详细规格](https://docs.nvidia.com/dgx/dgx-station-development-guide/latest/overview.html) · [NVIDIA 产品页](https://www.nvidia.com/en-us/products/workstations/dgx-station/) |
| Intel Arc Pro B50 | 已发布 | 单卡；PCIe 5 ×8；70 W | 16 GB GDDR6 | 224 | 170 TOPS 稠密 INT8 | [Intel 规格](https://dgpu-docs.intel.com/overview/architecture/gpu-architecture.html) |
| Intel Arc Pro B65 | 已发布 | 单卡；PCIe 5 ×16 | 32 GB GDDR6 | 608 | 197 TOPS 稠密 INT8 | [Intel 规格](https://www.intel.com/content/www/us/en/products/sku/245796/intel-arc-pro-b65-graphics/specifications.html) |
| Intel Arc Pro B70 | 已发布 | 单卡；PCIe 5 ×16 | 32 GB GDDR6 | 608 | 367 TOPS 稠密 INT8 | [Intel 规格](https://www.intel.com/content/dam/www/central-libraries/us/en/documents/2026-03/datasheet-b70-gpu.pdf) |
| AMD Radeon AI PRO R9600 | 已公布规格 | 单卡；主动散热；TBP 150 W | 32 GB GDDR6 | 640 | 199 TOPS 稠密 INT8 | [AMD 规格](https://www.amd.com/en/products/graphics/workstations/radeon-ai-pro/ai-9000-series/amd-radeon-ai-pro-r9600.html) |
| AMD Radeon AI PRO R9600D | 已公布规格 | 单卡；被动散热；TBP 150 W | 32 GB GDDR6 | 640 | 199 TOPS 稠密 INT8 | [AMD 规格](https://www.amd.com/en/products/graphics/workstations/radeon-ai-pro/ai-9000-series/amd-radeon-ai-pro-r9600d.html) |
| AMD Radeon AI PRO R9700S | 已公布规格 | 单卡；被动散热；TBP 300 W | 32 GB GDDR6 | 640 | 383 TOPS 稠密 INT8 | [AMD 规格](https://www.amd.com/en/products/specifications/professional-graphics.html) |

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
| 华为 Atlas 300I A2 32 GB | 已发布 | 单卡；两容量共用产品级算力栏 | 32 GB ECC（诊断资料归为 HBM） | 800 | 560 TOPS INT8；280 TFLOPS FP16 | [华为 Atlas 300I A2](https://e.huawei.com/cn/products/computing/ascend/atlas-300i-a2) |
| 寒武纪 MLU370-X4 | 已发布 | 单卡 | 24 GB LPDDR5 | 307.2 | 256 TOPS INT8 | [寒武纪 MLU370-X4](https://cambricon.com/index.php?a=lists&c=index&catid=371&m=content) |
| 摩尔线程 MTT S4000 | 已发布 | 单卡 | 48 GB GDDR6 | 768 | 200 TOPS INT8（规格书） | [摩尔线程 S4000](https://docs.mthreads.com/s4000/s4000-doc-online/product_specifications/) |
| 摩尔线程 MTT S5000 | 厂商称已商用 | 单卡；卡间互联另计 | 80 GB 显存 | 1600 | 1000 TFLOPS AI 稠密（年报表述） | [摩尔线程年报](https://mt-website-prod.mthreads.com/uploaded/investor/%E6%91%A9%E5%B0%94%E7%BA%BF%E7%A8%8B2025%E5%B9%B4%E5%B9%B4%E5%BA%A6%E6%8A%A5%E5%91%8A%E6%91%98%E8%A6%81.pdf) |
| 昆仑芯 R200 / R200-8F | 已发布 | 单卡；两 SKU 容量不同 | 16 / 32 GB GDDR6 | 512 | 256 TOPS INT8；128 TFLOPS FP16 | [昆仑芯 R200](https://www.kunlunxin.com/product/274.html) |
| Furiosa RNGD | 已发布 | 单 PCIe 卡 | 48 GB HBM3 | 1500 | 512 TOPS INT8；512 TFLOPS FP8 | [Furiosa RNGD 规格](https://developer.furiosa.ai/v2026.1.0-rc2/en/overview/rngd.html) |
| Rebellions Rebel100／REBEL-Quad | 官方披露 | 多 chiplet 加速卡；整卡规格 | 144 GB HBM3e | 4800 | 早期简报 2048 TFLOPS FP8／卡；与 [服务器页](https://rebellions.ai/rebellions-product/rebelserver/)口径有冲突，暂不作归一化比较 | [Rebellions REBEL-Quad](https://rebellions.ai/wp-content/uploads/2025/08/RebellionsREBEL-Quad_brochure_v1.1.pdf) |
| Tenstorrent Blackhole p100a | 已发布 | 单卡 | 28 GB GDDR6 | 448 | 664 TFLOPS BLOCKFP8 | [Tenstorrent Blackhole](https://docs.tenstorrent.com/aibs/blackhole/index.html) |
| Tenstorrent Blackhole p150a/b | 已发布 | 单卡；a/b 区分接口配置 | 32 GB GDDR6 | 512 | 664 TFLOPS BLOCKFP8 | [Tenstorrent Blackhole](https://docs.tenstorrent.com/aibs/blackhole/index.html) |
| Qualcomm Cloud AI 100 Ultra | 已发布 | 4 SoC 整卡合计；每 SoC 内存分域 | 128 GB LPDDR4X | 548 | 870 TOPS INT8；FP16 288 TFLOPS（产品表）/ 290（SDK） | [Qualcomm 产品表](https://www.qualcomm.com/data-center/products/cloud-ai-100-ultra) · [SDK 1.21](https://qualcomm.github.io/cloud-ai-sdk-pages/1.21/Getting-Started/Architecture/index.html) |
| Rebellions ATOM-Max CA25 | 厂商列为可询价 | 单 PCIe 卡；最大功耗 350 W | 64 GB GDDR6 | 1024 | 512 TOPS INT8；1024 TOPS INT4 | [厂商规格](https://rebellions.ai/rebellions-product/atom-max/) |
| Rebellions CA22（ATOM / ATOM+） | 已发布 | 单 PCIe 卡；区别于旧 CA02 | 16 GB GDDR6 | 256 | 128 TOPS INT8 | [厂商规格](https://rebellions.ai/rebellions-product/rbln-ca22/) · [SDK 支持](https://rebellions.ai/developers/) |
| d-Matrix Corsair | 厂商称向部分客户量产 | 单 PCIe 卡；2 GB SRAM、150 TB/s 另计 | 最高 256 GB LPDDR5 | 400 | 2400 TFLOPS MXINT8 稠密（厂商原单位） | [产品简报](https://www.d-matrix.ai/wp-content/uploads/2024/11/d-Matrix-Corsair-Product-Brief-FINAL.pdf) · [技术白皮书](https://d-matrix.ai/pdf/d-Matrix-WhitePaper-Technical-FINAL.pdf) · [生产公告](https://www.d-matrix.ai/announcements/d-matrix-corsair-ai-inference-platform-enters-full-production-to-meet-customer-demand/) |
| 华为 Atlas 300I A2 64 GB | 已发布 | 单卡；两容量共用产品级算力栏 | 64 GB ECC（诊断资料归为 HBM） | 1600 | 560 TOPS INT8；280 TFLOPS FP16 | [华为 Atlas 300I A2](https://e.huawei.com/cn/products/computing/ascend/atlas-300i-a2) · [Ascend 诊断指南](https://www.hiascend.com/document/detail/en/mindcluster/730/toolbox/toolboxug/toolboxug_0156.html) |
| 华为 Atlas 300I Pro | 已发布 | 单卡；最大 72 W | 24 GB LPDDR4X | 204.8 | 140 TOPS INT8；70 TFLOPS FP16 | [华为规格](https://e.huawei.com/cn/products/computing/ascend/atlas-300-ai) |
| 华为 Atlas 350 | 已公布规格 | 单板卡；950PR；≤600 W；UB 与 PCIe 不计入 | 112 GB HBM ECC | 1400 | 1561 TFLOPS MXFP4；425 TFLOPS FP16/BF16 | [华为卡规格](https://www.hiascend.com/hardware/accelerator-card) |
| 寒武纪 MLU370-X8 | 已发布 | 双芯整卡合计；250 W；200 GB/s 双向 MLU-Link 另计 | 48 GB LPDDR5 | 614.4 | 256 TOPS INT8；96 TFLOPS FP16/BF16 | [寒武纪规格](https://www.cambricon.com/index.php?a=lists&c=index&catid=406&m=content) |
| 寒武纪 MLU370-S4 | 已发布 | 单板卡；75 W | 24 GB LPDDR5 | 307.2 | 192 TOPS INT8；72 TFLOPS FP16/BF16 | [寒武纪规格](https://www.cambricon.com/index.php?a=lists&c=index&catid=367&m=content) |
| 寒武纪 MLU370-S8 | 已发布 | 单板卡；75 W | 48 GB LPDDR5 | 307.2 | 192 TOPS INT8；72 TFLOPS FP16/BF16 | [寒武纪规格](https://www.cambricon.com/index.php?a=lists&c=index&catid=367&m=content) |
| 昆仑芯 K100 | 已发布 | 单卡 | 8 GB HBM | 256 | 128 TOPS INT8；32 TFLOPS FP16 | [昆仑芯规格](https://www.kunlunxin.com/product/687.html) |
| 昆仑芯 K200 | 已发布 | 单卡 | 16 GB HBM | 512 | 256 TOPS INT8；64 TFLOPS FP16 | [昆仑芯规格](https://www.kunlunxin.com/product/686.html) |
| 昆仑芯 R100 | 已发布 | 单卡；75 / 100 W 档 | 12 GB GDDR6 | 384 | 128 / 170 TOPS INT8（对应 75 / 100 W） | [昆仑芯规格](https://www.kunlunxin.com/product/2837.html) |
| 昆仑芯 RG800 | 已发布 | 单卡 | 32 GB GDDR6 | 512 | 256 TOPS INT8；128 TFLOPS FP16 | [昆仑芯规格](https://www.kunlunxin.com/product/2842.html) |
| 算能 SC11 FP300 | 已发布；伙伴报道兼容验证 | 2 SoC 整卡合计；300 W；SG-Link 速率未核 | 最高 256 GB LPDDR5X | 约 1100（厂商称超过） | 超过 400 TOPS（原文 INT8/FP8）；超过 200 TFLOPS FP16/BF16 | [算能厂商申报](https://xh.21csp.com.cn/cxcp_2024/202409/2056.html) · [伙伴报道](https://bellcloudtech.com/news-info/22/) · [双 SoC 固件](https://github.com/sophgo/mcu/blob/master/SC11FP300/common.h) |
| 算能 SC7 FP300 | 已发布 | 多芯整卡合计；300 W；PCIe 4 ×16 | 128 GB LPDDR4X | 512 | 256 TOPS INT8；128 TFLOPS FP16/BF16 | [算能 SC7 V2.2](https://sophon-file.sophon.cn/sophon-prod-s3/drive/23/03/09/10/SOPHGO%20AI%E5%8A%A0%E9%80%9F%E5%8D%A1%20SC7%E7%B3%BB%E5%88%97%E4%BA%A7%E5%93%81%E4%BB%8B%E7%BB%8D_V2.2.pdf) |
| 算能 SC7 HP75 | 已发布 | 多芯整卡合计；75 W；PCIe 3 ×8；非 HP75-2 | 48 GB LPDDR4X | 205 | 96 TOPS INT8；48 TFLOPS FP16/BF16 | [算能 SC7 V2.2](https://sophon-file.sophon.cn/sophon-prod-s3/drive/23/03/09/10/SOPHGO%20AI%E5%8A%A0%E9%80%9F%E5%8D%A1%20SC7%E7%B3%BB%E5%88%97%E4%BA%A7%E5%93%81%E4%BB%8B%E7%BB%8D_V2.2.pdf) |
| Qualcomm Cloud AI 80 Ultra | 已发布 | 4 SoC 整卡合计；内存分域；150 W | 128 GB LPDDR4X | 548 | 618 TOPS INT8；222 TFLOPS FP16（产品表） | [Qualcomm 产品表](https://www.qualcomm.com/data-center/products/cloud-ai-100-ultra) · [SDK 1.21](https://qualcomm.github.io/cloud-ai-sdk-pages/1.21/Getting-Started/Architecture/index.html) |
| Qualcomm Cloud AI 100 Pro | 已发布 | 单 SoC；75 W | 32 GB LPDDR4X | 137 | 400 TOPS INT8 / 200 TFLOPS FP16（产品表）；375 / 125（SDK） | [Qualcomm 产品表](https://www.qualcomm.com/data-center/products/cloud-ai-100-ultra) · [SDK 1.21](https://qualcomm.github.io/cloud-ai-sdk-pages/1.21/Getting-Started/Architecture/index.html) |
| Qualcomm Cloud AI 100 Standard | 已发布 | 单 SoC；75 W | 16 GB LPDDR4X | 137 | 350 TOPS INT8 / 175 TFLOPS FP16（产品表）；325 / 110（SDK） | [Qualcomm 产品表](https://www.qualcomm.com/data-center/products/cloud-ai-100-ultra) · [SDK 1.21](https://qualcomm.github.io/cloud-ai-sdk-pages/1.21/Getting-Started/Architecture/index.html) |
| Qualcomm Cloud AI 80 Standard | 已发布 | 整卡；75 W | 16 GB LPDDR4X | 137 | 190 TOPS INT8；86 TFLOPS FP16（产品表） | [Qualcomm 产品表](https://www.qualcomm.com/data-center/products/cloud-ai-100-ultra) · [SDK 1.21](https://qualcomm.github.io/cloud-ai-sdk-pages/1.21/Getting-Started/Architecture/index.html) |
| HyperAccel Bertha 500 | 开发卡 / 演示；量产计划待兑现 | 单板卡；250 W；PCIe 5 ×16；2/4 卡逐层 PP | 128 GiB 开发卡；产品页 128 GB，最高 256 GB 目标 | 546 | 384 TFLOPS FP16；768 TFLOPS FP8 | [HyperAccel 规格](https://hyperaccel.ai/ha_product/bertha-500/) · [SDK 0.0.6](https://docs.hyperaccel.ai/0.0.6/) |

## 特殊存储架构

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| Groq GroqChip LPU | 特殊架构 | 单芯片；片上 SRAM | 230 MB SRAM | 80000（SRAM） | 750 TOPS INT8 | [GroqChip 数据表](https://groq.com/wp-content/uploads/2024/08/GroqChip%E2%84%A2-Processor-Product-Brief-v1.7.pdf) |
| Cerebras WSE-3 | 特殊架构 | 整片晶圆；片上 SRAM | 44 GB SRAM | 21000000（SRAM） | 125 PFLOPS 峰值 AI；精度口径核对 | [Cerebras WSE-3](https://www.cerebras.ai/press-release/cerebras-announces-third-generation-wafer-scale-engine) |


## 待核或低于双门槛

| 型号 | 阶段 | 计量范围 | 内存容量／类型 | 带宽（GB/s） | 峰值 AI 算力 | 一手来源 |
| --- | --- | --- | --- | ---: | --- | --- |
| Apple M3 / M3 Pro / M3 Max | 待核 | SoC；不拿媒体估算当官方量化峰值 | 最高 24 / 36 / 128 GB UMA | 100（M3，低于门槛）/ 150 / 400 | Neural Engine 数值未在对应一手规格核实 | [Mac M3 系列对照](https://www.apple.com/mac/compare/?modelList=MacBook-Air-M3-15%2CMacBook-Pro-16-M3) |
| Apple M3 Ultra | 待核 | SoC；统一内存 | 最高 512 GB UMA | 819 | 32 核 Neural Engine；该规格页未列 TOPS | [Apple Mac Studio 2025](https://support.apple.com/en-us/122211) |
| Apple M4 Pro / M4 Max | 待核 | SoC；Max 分 GPU SKU | 最高 64 / 128 GB UMA | 273 / 410–546 | 16 核 Neural Engine；不直接继承 M4 的 38 TOPS | [MacBook Pro M4 Pro/Max](https://support.apple.com/en-ie/121553) |
| Apple M5 | 待核 | SoC；统一内存 | 最高 32 GB UMA | 153 | 未核实可直接比较的绝对 AI 峰值 | [Apple M5](https://www.apple.com/newsroom/2025/10/apple-unleashes-m5-the-next-big-leap-in-ai-performance-for-apple-silicon/) |
| Apple M5 Pro / M5 Max | 待核 | 按芯片与 GPU 档位分别计；Mac mini M5 Pro 为 TB5 | 最高 64 / 128 GB UMA | 307（Pro）；460 / 614（Studio Max 32 / 40 核） | 未核实可比较绝对 AI 峰值 | [Mac mini 规格](https://www.apple.com/mac-mini/specs/) · [Mac Studio 规格](https://www.apple.com/mac-studio/specs/) |
| Apple M5 Ultra | 待核 | 64 / 80 核 GPU；UltraFusion die-to-die 不计入 | 最高 512 GB UMA；官网 256 / 512 GB 配 80 核 GPU | 1200 | 32 核 Neural Engine；绝对 AI 峰值待核 | [Mac Studio 规格](https://www.apple.com/mac-studio/specs/) |
| Apple M6（Mac mini） | 待核 | SoC；TB4；按容量档位 | 16 GB / 24 或 32 GB UMA | 153（16 GB）；170（24 / 32 GB） | 双 16 核 Neural Engine；绝对 AI 峰值待核 | [Mac mini 规格](https://www.apple.com/mac-mini/specs/) |
| 华为昇腾 950DT（2025 路线图） | 路线图 | 2025 keynote 封装目标，不同于云端 A5 | 144 GB HiZQ 2.0 HBM | 4000 | 950 家族 1 PFLOPS FP8 / 2 PFLOPS MXFP4；未落实此 SKU 对应口径 | [华为 2025 keynote](https://www.huawei.com/en/news/2025/9/hc-xu-keynote-speech) |
| 华为昇腾 960 / 970 / 980 | 路线图 | 2026 路线披露；960DT / 960PR 分别目标 2027 Q1 / Q3 | 待核 SKU | 待核 SKU | 不当实测或交付承诺 | [华为 2026 路线图更新](https://www.huawei.com/en/news/2026/9/hc-wang-keynote) |
| 沐曦 曦云 C500 | 待核 | 单卡；MetaXLink 不是显存带宽 | 64 GB 高带宽显存 | 官方产品页未量化 | 官方产品页未量化 | [沐曦 C500](https://www.metax-tech.com/prod.html?cid=107&id=21) |
| Qualcomm Dragonfly AI200 | 少量云端评估 | 单卡容量；414 TB/s 为 56 卡机架汇总 | 768 GB LPDDR5X | 独立单卡物理口径待核 | 绝对算力待核 | [Qualcomm 规格](https://www.qualcomm.com/data-center/products/qualcomm-dragonfly-ai200) · [2026-11-17 Cirrascale](https://www.cirrascale.com/press/qualcomm-and-cirrascale-ai-inference) |
| Qualcomm Dragonfly AI250 | Gen1 硅片验证；计划 2027 H1 送样 | HBC 133 TB/s 每卡有效值，不是 DRAM 物理带宽 | 厂商称 768 GB | 物理带宽待核 | 绝对算力待核 | [Qualcomm 规格](https://www.qualcomm.com/data-center/products/qualcomm-dragonfly-ai250) · [2026-11-02 HBC 验证](https://www.qualcomm.com/news/onq/2026/11/qualcomm-hbc-validated-silicon-ai-inference) |
| SambaNova SN40L | 待核 | 单 RDU 多层内存；不得合并带宽 | 64 GB HBM + 768 GB DDR + 520 MB SRAM | 未核到同口径分层带宽 | 厂商整机 BF16 数据不替代单芯片 | [SambaNova SN40L 规格](https://sambanova.ai/hubfs/SambaRack%20data%20sheet%20template%2007%2001%2025%20%281%29.pdf) |
| Hailo-10H | 低于算力门槛 | 边缘模组，PCIe 不能当内存带宽 | LPDDR4/4X | 未核实达到 128 GB/s | 40 TOPS INT4 | [Hailo-10H 产品页](https://hailo.ai/products/ai-accelerators/hailo-10h-ai-accelerator/) |
| 昆仑芯 M100 | 待核 | 厂商称已公开展示 | 待核 | 待核 | 待核 | [百度 2026 Q3 官方披露](https://ir.baidu.com/news-releases/news-release-details/baidu-announces-third-quarter-2026-results) |
| 沐曦 曦云 C550 | 待核 | OAM；最大 450 W；896 GB/s 为八卡 MetaXLink 口径 | 64 GB | 物理内存带宽待核 | 峰值待核 | [沐曦规格](https://www.metax-tech.com/prod.html?cid=107&id=35) |
| 华为昇腾 950DT（ModelArts A5） | 云端配置披露 | 2026-10-21 A5 1D PoD；单封装 UMA | 96 GB | 最高 4000 | 该配置峰值待核；不继承 144 GB 路线值 | [华为云 A5 资源规格](https://support.huaweicloud.com/resmgmt-modelarts/resmgmt-a5_0002.html) |
| 沐曦 曦云 C600 | 待核 | OAM 2.0；最大 1000 W | 高带宽内存；容量待核 | 物理内存带宽待核 | 峰值待核 | [沐曦规格](https://www.metax-tech.com/prod.html?cid=107&id=68) |
| HyperAccel Bertha 100 | 低于带宽门槛 | 单卡；不是 Bertha 500 | 16 GB LPDDR5X | 64 | 带宽未过门槛 | [HyperAccel 规格](https://hyperaccel.ai/ha_product/bertha-100/) |
| SpacemiT K3 | 低于两项门槛 | 单 SoC；多节点不合并 | LPDDR5 | 51 | 60 TOPS（厂商口径） | [SpacemiT K3 数据手册](https://github.com/spacemit-com/docs-chip/blob/main/en/key_stone/k3/k3_docs/k3_ds.md) |
| Apple M1 Ultra | 低于门槛；已发布 | SoC；双 die 统一内存；已列 Neural Engine 算力低于门槛 | 128 GB UMA | 800 | 22 TOPS Neural Engine；精度未列 | [Apple M1 Ultra](https://www.apple.com/newsroom/2022/03/apple-unveils-m1-ultra-the-worlds-most-powerful-chip-for-a-personal-computer/) |
| Apple M2 Ultra | 低于门槛；已发布 | SoC；双 die 统一内存；已列 Neural Engine 算力低于门槛 | 192 GB UMA | 800 | 31.6 TOPS Neural Engine；精度未列 | [Apple M2 Ultra](https://www.apple.com/newsroom/2023/06/apple-introduces-m2-ultra/) |
| Apple M4（Mac mini） | 低于门槛；已发布 | SoC；内存带宽和已列 Neural Engine 算力均低于门槛 | 32 GB UMA | 120 | 38 TOPS Neural Engine；精度未列 | [Apple M4](https://www.apple.com/newsroom/2024/05/apple-introduces-m4-chip/) · [Mac mini M4 规格](https://support.apple.com/en-us/121555) |
| AMD Ryzen AI Max+ 395 | 低于门槛；已发布 | SoC；已列 NPU 算力低于门槛 | 128 GB LPDDR5X UMA | 256 | 50 TOPS NPU | [AMD Ryzen AI Halo 配置](https://developer.amd.com/playbooks/user-guide/) · [AMD Ryzen AI Max+ 395](https://www.amd.com/en/products/processors/laptop/ryzen/ai-300-series/amd-ryzen-ai-max-plus-395.html) |
| Qualcomm Snapdragon X Elite（X1E-84/80/78） | 低于门槛；已发布 | SoC；按 OEM 配置；已列 NPU 算力低于门槛 | LPDDR5X UMA | 135 | 45 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| Qualcomm Snapdragon X Plus（X1P-66/64/46/42） | 低于门槛；已发布 | SoC；按 OEM 配置；已列 NPU 算力低于门槛 | LPDDR5X UMA | 135 | 45 TOPS NPU | [Qualcomm PC SKU 规格](https://www.qualcomm.com/laptops/products) |
| NVIDIA Jetson Orin Nano Super 8GB | 低于门槛；已发布 | 开发套件，Super 档；算力和内存带宽均低于门槛 | 8 GB LPDDR5 | 102 | 67 AI TOPS（官方档位） | [NVIDIA Jetson 模块对照](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/) |
| AMD Ryzen AI Max+ PRO 495 | 低于门槛；官方披露 | SoC；AMD Halo 192 GB 型；已列 NPU 算力低于门槛 | 192 GB LPDDR5X UMA | 273 | 55 TOPS NPU | [AMD Ryzen AI Halo 配置](https://developer.amd.com/playbooks/user-guide/) · [AMD Ryzen AI Max+ PRO 495 规格](https://www.amd.com/en/products/processors/laptop/ryzen-pro/ai-max-pro-400-series/amd-ryzen-ai-max-plus-pro-495.html) · [AMD Ryzen AI Max PRO 400 发布](https://www.amd.com/en/blogs/2026/amd-powers-next-generation-agent-computers-with-new-ryzen-ai-hal.html) |
| Intel Core Ultra 7 258V | 低于门槛；已发布 | SoC；封装内 128-bit；带宽理论值；已列 GPU 和 NPU 的独立算力均低于门槛 | 32 GB LPDDR5X-8533 | 136.5 | 64 TOPS INT8 GPU；47 TOPS INT8 NPU（不相加） | [Intel ARK](https://www.intel.com/content/www/us/en/products/sku/240957/intel-core-ultra-7-processor-258v-12m-cache-up-to-4-80-ghz/specifications.html) · [Intel Lunar Lake](https://cdrdv2-public.intel.com/824443/2024_Intel_Tech%20Tour%20TW_Lunar%20Lake%20Architecture%20Session%20Highlights-3.pdf) |
| AMD Ryzen AI Max PRO 490 | 低于门槛；官方披露 | SoC；256-bit 满宽计算值；依 OEM 配置；已列 NPU 算力低于门槛 | 最高 192 GB LPDDR5X UMA | 约 273 | 50 TOPS NPU | [AMD 490 规格](https://www.amd.com/en/products/processors/laptop/ryzen-pro/ai-max-pro-400-series/amd-ryzen-ai-max-pro-490.html) · [AMD 规格对照](https://www.amd.com/en/products/specifications/processors.html) |
| 算能 SG2380 | 低于门槛；官方披露；Oasis 板暂停 | 单 SoC；256-bit 满宽理论值；未核出货；算力低于门槛 | 最高 128 GB LPDDR5；支持 LPDDR4/X | 204.8（LPDDR5-6400） | 32 TOPS INT8；16 TFLOPS FP16 | [Milk-V SoC 规格](https://milkv.io/chips/sg2380) · [Oasis 暂停公告](https://community.milkv.io/t/introducing-the-milk-v-oasis-with-sg2380-a-revolutionary-risc-v-desktop-experience/780/196) |
| 算能 SC5-3-H | 低于门槛；已发布 | 整卡；75 W；PCIe 3 ×8；已列 INT8 算力低于门槛 | 36 GB LPDDR4X | 192 | 52.8 TOPS INT8 | [算能 SC5 V1.0](https://sophon-file.sophon.cn/sophon-prod-s3/drive/21/11/19/10/%E6%99%BA%E7%AE%97%E5%8D%A1SC5%E7%B3%BB%E5%88%97%E4%BA%A7%E5%93%81%E5%BD%A9%E9%A1%B5_V1.0.pdf) |

## 本轮口径说明

- **双芯板卡**：SC11 的 SG-Link 是跨卡连接，不是 1.1 TB/s 板载 DRAM；同一张卡也有两个 DDR 配置域。Atlas 350 的 UB 公布值为每卡双向 424 GB/s（2 卡）/ 318 GB/s（4 卡全互联），不属于显存带宽，也不能套给 Atlas 150、全部 950PR 或 A5。一卡多芯的部署对照见 [[大模型推理性能与成本基线]]。
- **分层内存**：DGX Station 的 748 GB 是两级可寻址容量，并非 748 GB 都有 HBM 带宽。表采用 NVIDIA 详细规格的 LPDDR5X 396 GB/s；[开发指南简介](https://docs.nvidia.com/dgx/dgx-station-development-guide/latest/Intro.html) 写 384 GB/s，[旧公告](https://investor.nvidia.com/news/press-release-details/2025/NVIDIA-Launches-AI-First-DGX-Personal-Computing-Systems-With-Global-Computer-Makers/default.aspx) 曾写 784 GB。分别保留，不能把数值拼为同一配置。
- **标称与交付**：Bertha 500 的 128 GiB 来自版本化 SDK；[11 月生产配置报道](https://www.thelec.net/news/articleView.html?idxno=15561) 提到 192 GB，256 GB 是产品页上限。已演示和量产计划都不是库存、报价或延迟验证。Apple 算力继续待核，不从其他 SKU 借值。

## 下轮优先补查

1. 国产卡：昇腾 910B/C、950 各板型、昆仑芯 M100／M300、沐曦 C550/C600、寒武纪后续产品、燧原、壁仞、天数智芯、海光 DCU、芯原以外的推理加速器。优先找产品规格、开发文档、公开披露或送样公告；媒体只作为查找线索。
2. 国际路线图与新入场：NVIDIA Rubin Ultra、AMD MI450X/MI500、Intel Crescent Island、AWS Trainium 后续、Meta MTIA、Google TPU 新档位、d-Matrix、SambaNova、日韩与欧洲厂商。核对披露、样品、部署、商业可用性，不从预期时间推定量产。
3. 端侧与现货补齐：Apple 各 SoC 的可追溯 AI 峰值、Intel/AMD/OEM 的实际内存位宽及通道、GeForce/Radeon/Arc 旧款和移动档、机器人模组、独立 NPU。淘汰项用硬证据说明原因，不因没有披露而标成不达标。
4. 待补字段：板卡功耗、标准接口与规模、软件栈及端到端实测；不以规格值直接推断 LLM 吞吐或成本。
