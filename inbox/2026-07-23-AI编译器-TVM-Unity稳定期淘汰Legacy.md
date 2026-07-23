# TVM Unity 进入稳定期，逐步淘汰 Legacy 组件
- 来源: https://whimsy.apache.org/board/minutes/TVM.html
- 日期: 2026-07-23
- 标签: #AI编译器 #TVM #Unity #Relax #TIR

## 摘要
Apache TVM 在完成 Unity (Relax) 转型后进入稳定发展阶段，社区正在推动淘汰旧的 schedule-based 流程，引入类 Triton 的 TIR 语法糖以支持 FlashAttention、T-MAC 等现代算子。

## 要点
- TVM Unity 统一了 computational graph、tensor program、runtime、hardware primitives 四层抽象
- 新 TIR 语法糖将 schedule 转为 annotation（如 Pipeline、Layout Transform），更像 Triton 编程风格
- 逐步淘汰 Relay 等 legacy 组件，降低测试和技术耦合成本
- PyTorch 集成持续改进，Relax 前端成熟度提升
