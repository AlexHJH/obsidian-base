# TileLang 第 39 周：FP4→FP8 融合提速 5.38×，TileSight 性能模型开源

- 来源: https://kubuds.io/en/tilelang/weekly/2026/W39.html
- 日期: 2026-09-25（统计周期 09-21 ~ 09-25）
- 标签: #AI编译器 #TileLang #TVM #性能建模 #AMD

## 摘要
TileLang 主仓与 tile-ai 生态本周同时推进：主仓把「E2M1(FP4) → FP32 → E4M3(FP8)」投影链融合成寄存器内操作（H100 上 2.97–5.38×）；tile 级解析性能模型 TileSight 按论文承诺开源；TileRT v0.1.6 把 GLM-5.2/5.3 带到 AMD MI350X/MI355X；TileOPs 一周约 21 次合并，并把 roofline 公式收敛为唯一执行来源。

## 要点
- FP4→FP8 融合（#3204）：每 4 个元素两条 __byte_perm，覆盖标量 + 5 种向量宽度、纯寄存器无新分配；寄存器 128→156 无 spill；Blackwell 执行与整机服务未验证
- TileSight 开源（09-23，177 文件 / 约 5.7 万行，MIT）：20+ 硬件 profile、多级缓存 reuse-distance 模型与流水线重叠分析；论文口径单卡 kernel 延迟 MAPE 12.35%，32 卡集群融合内核 16.18%、vLLM 端到端 13.52%
- TileRT v0.1.6（09-24 一天合入两个 release PR）：新增 ROCm 路径的 GLM-5.2/5.3 支持与 PD 分离更新，但 GitHub Releases/tag 仍停在 v0.1.5.post2——发布页滞后，公开版本未更新
- 质量侧：TileOPs 修正 175 个算子的 15 类 roofline 公式错误并将 manifest 公式设为单一来源；主仓 14 次合并含 Fuzzer ICE 修复与 AllReduce stride 校验（拦截「能跑但静默算错」）
- DeltaNet 推理算子在 H200 上相对 FLA 最高 3.7×
- 关联 [[AI编译器2026格局]] [[推理引擎格局-vLLM-SGLang]]
