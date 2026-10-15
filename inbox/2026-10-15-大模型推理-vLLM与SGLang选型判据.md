# 推理引擎选型判据：前缀重合率 >60% 选 SGLang，否则看生态
- 来源: https://developers.redhat.com/articles/2026/10/14/vllm-vs-sglang-choosing-the-right-inference-engine-for-your-workload ; https://www.spheron.network/blog/vllm-vs-sglang-2026
- 日期: 2026-10-14
- 标签: #大模型推理 #vLLM #SGLang #部署

## 摘要
Red Hat 开发者博客（10/14）与第三方基准给出同一判据：vLLM 的 PagedAttention 与 SGLang 的 RadixAttention 在「提示词互不重复」的批量任务上吞吐差距在 5% 以内；一旦请求共享前缀（系统提示、RAG 文档、工具定义）比例超过 60%，SGLang 的延迟优势明显。

## 要点
- 决定因素不是引擎强弱而是负载画像：前缀重合率高 → 缓存复用收益大；唯一提示词为主 → 比模型支持广度与运维复杂度
- 工程现实：模型架构适配仍是主要坑点（Qwen3.8-27B 一类混合模型在 vLLM 0.30 需下调序列上限才能起、SGLang 0.5.20 曾静默限并发 20），TensorRT-LLM 稳定版在部分新架构上直接不加载
- 结构趋势：PD 分离（prefill/decode disaggregation）已成默认 playbook（DistServe 回顾），多轮场景出现 PPD/AMPD 一类变体（arXiv 2603.13358 ICML 2026、arXiv 2602.14516），把「增量 prefill」与首次 prefill 区别对待
- 读法：选型要先量化自己流量的前缀重合率与 PD 分离收益，再看引擎；与本地小模型场景（[[MLX生态2026]]）的批处理饱和判据同构
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[MLSys2026论文精选]]
