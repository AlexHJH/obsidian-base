# 编译器后端动态（2026-10-15）：AMD 分片 GEMM、SM100 BF16 折叠、Ascend 950 FlashMLA

- 来源: https://ruyisdk.cn/t/topic/2892
- 日期: 2026-10-15
- 标签: #AI编译器 #Triton #TileLang #AMD #Ascend

## 摘要
一期中文编译技术日报把当日增量收敛成三件事：Triton 的 AMD 后端给分片 GEMM 做了调度/预取/Subtile 融合三项优化；NVIDIA 侧修 SM100 的 BF16→FP32 累加链成本模型；TileLang 在 Ascend 950 上手写 FlashMLA 稀疏注意力，性能贴近参考实现。

## 要点
- Triton AMD：全局/局部加载与计算交错以重叠访存；LDS 预取只对前导点积操作数做回边预取；Subtile 融合把共享描述符的多次加载合成一次 TDM 拷贝
- Triton SM100：扩展 `isFusedHalfAddition` 识别 BF16 扩展，使 BF16 转换折叠为混合精度加法（成本模型修正，非新算子）
- TileLang #3464 Ascend 950：显式 AIC/AIV 分区 + gather2 + online softmax + skip-scale buffer，prefill 峰值 398.6–404.9 TFLOPS，偏差约 1.1%
- 读法：三件事同向——后端竞争已从「支持该硬件」转到「成本模型与流水线排布是否精确」，误差 1% 级才算可用
- 关联 [[AI编译器2026格局]]、[[AI芯片推理硬件2026全景]]
- 口径：条目取自该日报摘要，未核对原始 PR 与跑分脚本
