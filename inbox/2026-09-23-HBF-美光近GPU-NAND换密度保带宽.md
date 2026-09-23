# 美光探索「近 GPU NAND」：牺牲密度换 I/O 带宽
- 来源: https://www.ithome.com/0/997/668.htm ; https://www.trendforce.cn/industry-news/semiconductors/20260904-7768.html
- 日期: 2026-09-02
- 标签: #HBF #NAND #美光 #GPU封装

## 摘要
DigiTimes 报道美光在研「近 GPU NAND」：高耐久 NAND 模块布置到离 GPU 更近处，让 GPU 直接访问数百 GB NAND 池，形态可直接放进 GPU 封装内或 PCB 上，工作方式类比 HBM/GDDR7。与 TLC/QLC 追密度相反，这颗芯片主动降低存储密度，把资源压在 I/O 速度、带宽与读延迟上。

## 要点
- 定位是显存与普通存储之间的高速缓冲层，面向「非低延迟但高带宽」负载；落地后 LLM 推理不再被显存容量卡住
- 路线分歧：闪迪/SK 海力士的 HBF 走 OCP 开放标准 + UCIe（最高 512GB、约 0.4–3.0TB/s），美光走自研器件 + 近封装集成，且未加入 HBF 联盟
- 与三星 zNAND-O / zHBM、铠侠 GP1 并列，说明「HBM 之后必有分层」已是全行业共识，分歧在接口形态与是否开放
- 待观察：耐久性指标未公开、是否真进封装而非仅板载、与 HBM 的地址空间如何统一（决定软件栈改造量）
- 关联 [[HBF-NAND-AI推理内存2026]]、[[AI芯片推理硬件2026全景]]
