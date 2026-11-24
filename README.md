# KePu · 医疗科普智能体产品调研

这是一个面向医生、患者和内容/运营团队的医疗科普智能体产品研究与概念设计交付物。

## 结论先行

基于当前已知的公司能力（科普内容生产与视频拍摄），优先验证“医生审核的内容生产 → 患者互动学习 → 问题回流 → 医生/直播跟进”的知识服务闭环，再逐步扩展至更复杂的患者服务场景。

## 交付物

- [`index.html`](./index.html)：可直接部署到 GitHub Pages 的交互式网页报告。
- `research_notes/kepu-primary-research.md`：第一方资料与开源项目核验记录（由研究代理补充）。

## 报告覆盖

- 医生端：选题简报、脚本/分镜、证据卡、重点句、审核与问题队列。
- 患者端：互动视频、小测、分支任务、理解反馈、收藏/提问与升级服务。
- 直播：直播前问题采集、实时问题分流、AI 共主持、回放章节与内容再生产。
- 三类 Agent：科普编导、患者陪学、直播共创。
- 开源轮子评估：H5P Core、LiveKit、Medplum；Owncast 作为更轻量的直播/聊天备选。
- P0/P1/P2 路线图、指标体系、服务分流和可推翻结论的证据。

## 落地条件

网页呈现的是面向医生客户的产品研究与概念设计。正式落地前，建议围绕知识来源、医生审核、用户授权、数据留存、内容安全、真人升级和医院接口开展联合评审。

## 开源参考

- [H5P Core](https://github.com/h5p/h5p-php-library)：互动内容核心库，可承载 Interactive Video、Branching Scenario、Quiz、Game Map 等内容类型，GPL-3.0。
- [LiveKit](https://github.com/livekit/livekit)：WebRTC/实时音视频与直播底座，Apache-2.0。
- [Medplum](https://github.com/medplum/medplum)：医疗应用开发平台，FHIR/身份/权限/数据对象，Apache-2.0。
- [Owncast](https://github.com/owncast/owncast)：更轻量的自托管直播与聊天备选，MIT。

统计口径与访问日期见网页报告。Star 用于判断社区关注度；采购与技术选型还需结合许可证、部署成本、医疗数据治理和目标工作流评估。
