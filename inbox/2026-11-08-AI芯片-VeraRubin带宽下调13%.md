# Vera Rubin 悄悄把 HBM4 带宽从 22 降到 19.2 TB/s，NVL8 掉 26%
- 来源: https://wccftech.com/nvidia-quietly-lowers-vera-rubin-hbm4-bandwidth-by-up-to-26-nvlink-speeds-also-reduced
- 日期: 2026-09-01～09-11 之间改版，10 月由存档页比对发现
- 标签: #NVIDIA #Rubin #HBM4 #带宽

## 摘要
NVIDIA 在没有发布会也没有说明的情况下改写了 Vera Rubin 产品页：单颗 Rubin GPU 的 HBM4 带宽 22→19.2 TB/s（-12.7%）、NVLink 6 3.6→3 TB/s（-16.7%），容量 288GB 不变；HGX Rubin NVL8 降得最狠，整机 176→130 TB/s（-26.1%），HBM 容量 2.3→2 TB，FP8 训练 140→130 PFLOPS，只有 scale-out 网络反向 +12.5%。

## 要点
- 换算到引脚：8 组堆叠下 22 TB/s 需约 10.75 Gbps/pin，19.2 TB/s 只需约 9.375 Gbps/pin；而三星/SK 海力士已披露 11.7（三星最高 13）Gbps、美光 >11 Gbps——指标下调不是器件做不到，而是给认证与产出留余量
- 口径不自洽：8×19.2≈154 TB/s，页面却写 130 TB/s；有分析按 2TB/8 卡=250GB/GPU 推测实际是 7 组有效堆叠，NVIDIA 未解释，且 HGX 页仍留着旧的每卡数字
- 与 Rubin Ultra 转 8-Hi HBM4 同源：12-Hi 堆叠发热与良率顶不住，于是层数和速率一起回摆，本质是「按供给可得性定价」而不是设计失误
- 推理侧含义：每 token 的 KV 带宽预算直接按 13% 缩水，scale-up NVLink 再砍 17% → MoE expert-parallel 的 all-to-all 更吃紧；对纸面比较，AMD MI455X 在单卡带宽上已反超（未实测）
- 口径：产品页存档对比 + Wccftech/TechRadar/TweakTown/Hardware Busters 多方交叉，均为规格表数字，NVIDIA 未说明也无实测

关联：[[AI芯片推理硬件2026全景]]、[[HBF-NAND-AI推理内存2026]]
