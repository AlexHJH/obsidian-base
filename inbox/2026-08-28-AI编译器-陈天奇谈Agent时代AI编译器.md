# 陈天奇：Agent 时代，如何重新思考 AI 编译器
- 来源: https://view.inews.qq.com/a/20260806A0B6M900
- 日期: 2026-08-28
- 标签: #AI编译器 #TVM #TIRx #Agent

## 摘要
TVM/XGBoost/MLC LLM 创始人陈天奇在 HyperAI「Meet AI Compiler」第 9 期沙龙（8/1 北京）分享：Agent 可直接编程的时代编译器仍有价值，但要「分层」——稳定核心（ABI/IR/跨语言）+ 由 Agent 驱动的快速迭代层，靠 TVM FFI 规范模块边界。

## 要点
- TVM FFI 定义算子/模块调用边界，已整合 CuTeDSL、FlashInfer、TensorRT，模块可灵活替换
- TIRx 让不同硬件注册自己的 dispatch rule，Agent 按硬件特点生成特化编译逻辑
- 方向：跨语言（Python/C++/Rust）、MegaKernel（图级+内核级联动）、编译器「做薄」分层
- 判断：Agent 写 Compiler 已非「可能性」而是进行时，关键在模块化与好的 IR 基础设施
- 相关: [[AI编译器2026格局]]
