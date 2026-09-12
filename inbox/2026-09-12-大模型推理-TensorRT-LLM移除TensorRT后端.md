# TensorRT-LLM 1.2：移除 TensorRT 后端，PyTorch 成唯一执行后端
- 来源: https://nvidia.github.io/TensorRT-LLM/release-notes.html
- 日期: 2026-09-11
- 标签: #大模型推理 #TensorRTLLM #NVIDIA #PyTorch

## 摘要
TensorRT-LLM 1.2 把 PyTorch 定为唯一执行后端，AOT 编译的 TensorRT 后端被彻底移除：`LLM(backend="tensorrt")` 直接抛 ValueError，构建工具链一并删除。

## 要点
- 移除清单：`TrtLlmArgs`、`tensorrt_llm._tensorrt_engine.LLM`、`trtllm-build/refit/prune` CLI、`--backend tensorrt`、各模型 `convert_checkpoint.py`，`tensorrt` pip 依赖不再安装
- 早有预告：v1.3.0rc20 即声明为「最后一个支持 TensorRT 后端的版本」，需按迁移指南改造现场脚本
- 1.2 同步新增单节点 DGX Spark 支持（beta，验证 GPT-OSS-120B MXFP4、Llama-3.3-70B、Qwen3 系列 FP16/FP8/NVFP4）
- 含义：NVIDIA 自家引擎转向即时执行 + 运行时 autotuning，与 vLLM/SGLang 的工程模型趋同，shape 特化的 engine build 收益让位于可维护性
- 关联 [[推理引擎格局-vLLM-SGLang]]、[[AI芯片推理硬件2026全景]]
