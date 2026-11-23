# PTX 9.5：locality domain 与 tensormap 分配类进了 ISA，调度语义从 host 挪进 kernel
- 来源: https://docs.nvidia.com/cuda/parallel-thread-execution/index.html
- 日期: 2026-11-02 (PTX ISA 9.5 文档版本)
- 标签: #NVIDIA #PTX #TMA #GPU调度 #AI芯片

## 摘要
PTX ISA 9.5 的新增项几乎全在「运行时可见性」而非算力：新增 `%locality_domain_id` 与 `%locality_domain_supplemental_sm` 两个只读特殊寄存器（sm_100+），让 persistent kernel 自己判断跑在 locality domain 的保证最小 SM 集内还是补充 SM 上；TMA 描述符侧新增 `.alloc_class`（`.alloc_small/.alloc_large/.alloc_same`）与 `tensormap.query.alloc_class_large`。

## 要点
- `%locality_domain_supplemental_sm` 返回 0/1：0 = 属于该 locality domain 的 guaranteed minimum SM 集，1 = 集外补充 SM。同一 cluster 内不同 CTA 可能读到不同值（cluster 可跨两类 SM 光栅化）——这把「哪些 SM 是保证的、哪些是超发的」暴露给了设备端调度
- 动机写得很直白：让运行时/persistent kernel 不依赖 host 侧 launch API 就能按 locality domain 均衡分发负载。等于把一部分 floorsweeping/MIG 式的资源可见性下放到 ISA
- TMA 侧：`tensormap.replace.alloc_class` 默认为 `.alloc_small`，只有显式指定才升到 large —— 描述符容量不再只能靠 host 侧 API 猜，kernel 可原地改类并查询
- 其余新增是同步/追踪细节：`fabric.submit.commit_group` + `fabric.wait.sync_restrict::read N`（旧 `::reads` 废弃）、`cp.async.bulk.wait_group.sync_restrict::proxy::async`、`fence.alias.sync_restrict::read.release.sys`、`trace.mark` + `.metadata_section`、`sin/cos` 的 `.2pi` 限定
- 对自研后端/编译栈的含义：locality domain 是「物理拓扑 → 逻辑资源池」的一层新抽象，若要做 RISC-V 侧的类 GPU 多核分区，这套「保证集 + 补充集」的语义比纯 SM mask 更好表达
- 关联 [[NVIDIA-Tensor-Core-TMA与AI指令体系]]、[[AI编译器2026格局]]
