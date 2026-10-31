# MorphX（SOSP'26）：可变形内核，kernel 运行中让出/收回算力

- 来源: https://dl.acm.org/doi/10.1145/3830418.3843904
- 日期: 2026-09-28
- 标签: #SOSP26 #GPU #多任务 #抢占 #调度

## 摘要
传统 kernel 一旦 launch 就锁住算力，只能靠 draining 或被抢占才能让给前台任务。MorphX 提出 morphable kernel：编译期把普通 kernel 改写成运行中可动态 yield / reclaim 计算资源的形态（无需额外 launch），再用 in-place profiling 按实时资源争用调调度。同等前台延迟下，后台吞吐提升 2.24×。

## 要点
- 抽象：kernel 自身可变形态，抢占不再等于「重新 launch / draining」，省掉调度延迟
- 实现：编译期透明改写 + 运行期原地剖析（in-place profiling）判断争用程度，不依赖硬件抢占支持
- 数据：与现有系统相比后台任务吞吐 2.24×（同前台延迟约束）；多资源利用率全场最高
- 意义：把「共存/让路」从 OS、驱动的职责下沉到 kernel 可塑性，与 MPS/MIG 的静态粗粒度切分是两条路
- 与同届 Meld 分工：Meld 处理单 kernel 内的动态性（跨 SM 融合），MorphX 处理 kernel 之间的共存
- 口径：数字取自 SOSP'26 论文摘要，未复现

关联：[[MLSys2026论文精选]]、[[AI芯片推理硬件2026全景]]
