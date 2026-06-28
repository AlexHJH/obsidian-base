# From Tokens to Layers：分层预填重构 MoE 服务调度
- 来源: https://arxiv.org/abs/2510.08055
- 日期: 2026-06-28
- 标签: #大模型推理 #MLSys2026 #MoE #调度 #能耗

## 摘要
首尔大学 MLSys 2026 论文，提出 layered prefill：将模型纵向切分为连续层组，跨层组交错 prefill 与 decode，消除 chunked prefill 在 MoE 上的冗余专家权重重载（内存流量 +39%），改善 TTFT–TBT 帕累托前沿。

## 要点
- 痛点：chunked prefill（按 token 切分）稳定 TBT，但 MoE 下重复加载专家权重，内存流量增 39%、能耗上升
- 解法：调度轴从 token 转为 layer，层组间交错 prefill/decode，保持 decode 无 stall
- 收益：降低专家加载流量与能耗，TTFT–TBT 帕累托前沿全面优于 chunked prefill
- 开源：github.com/scale-snu/layered-prefill；关联 [[推理引擎格局-vLLM-SGLang]]
