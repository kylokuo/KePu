# 医疗科普智能体 / 医患互动 / 游戏化健康教育 / 医疗直播：第一方资料调研

> 访问日期：2026-11-24（Asia/Shanghai）。
>
> 证据边界：本笔记只把项目官方文档、官方开发者文档、项目自有 GitHub 仓库和 GitHub 官方 REST API 作为主要证据。Stars、`updated_at`、`pushed_at` 为 GitHub API 在访问日返回的快照；“部署难度”“可直接复用/需二次开发”“风险”为基于官方材料的工程判断，不是项目方承诺。医疗安全、隐私、知情同意、内容审核、医疗责任和当地监管合规仍需单独评估。

## 先给结论

这四个方向不是一个单体产品问题，而是四层能力的组合：

1. **医疗数据与权限层**：优先看 Medplum；如果需要完整 EHR、排班、账单和现成患者门户，再看 OpenEMR。
2. **对话与流程层**：Rasa 的经典开源框架仍可作为对话编排参考，但官方仓库明确处于 maintenance mode；不宜未经生命周期评估就把它作为新项目唯一核心。
3. **科普内容与游戏化层**：H5P 的 Interactive Video、Branching Scenario、Quiz、Game Map 等内容类型适合把健康教育拆成可复用、可交互、可评估的内容包。
4. **直播/实时互动层**：LiveKit 提供 WebRTC SFU、房间、数据通道、鉴权、加密、录制/导入导出等底座，但它不替代医疗直播所需的主播资质审核、评论审核、病例脱敏、知情同意和合规留痕。

### 最值得参考的 3 个

| 排名 | 实现 | 适合承担的角色 | 选择理由 | 主要保留意见 |
|---|---|---|---|---|
| 1 | [Medplum](https://github.com/medplum/medplum) | 医疗数据、FHIR、身份与权限、医生/患者应用、服务端 Bot | 医疗领域原生，官方仓库同时提供 FHIR API、SMART-on-FHIR/OAuth、临床数据仓库、React UI、Bots 和应用层能力 | 它是开发平台，不是已经完成的“科普智能体”；上线仍需自行做内容治理、模型安全、审计和产品体验 |
| 2 | [H5P Core](https://github.com/h5p/h5p-php-library) | 游戏化健康教育和交互式科普内容 | 内容类型丰富，官方支持 Interactive Video、Branching Scenario、Quiz、Game Map，并可接入 WordPress、Moodle、Drupal 或 LTI | Core 是 GPL-3.0，且需要宿主平台实现 `H5PFrameworkInterface` 等接口；不是拿来即用的独立 SaaS |
| 3 | [LiveKit](https://github.com/livekit/livekit) | 医疗直播、医生/患者实时音视频、直播间内 AI/互动组件 | 开源实时底座成熟，官方明确支持 WebRTC SFU、JWT、E2EE、moderation API、data tracks、Docker/Kubernetes | 只解决实时传输与房间能力；医疗直播的内容、身份、权限、审核、录制保管和合规边界要自行设计 |

**不把 Rasa 排入前三的原因**：它的 Stars 和生态仍然很强，但官方仓库明确写明 Rasa Open Source 处于 maintenance mode，未来重点转向 Hello Rasa/CALM；这会把新项目的长期维护和版本路线变成主要风险。[官方 README](https://github.com/RasaHQ/rasa/blob/3.6.x/README.md)，访问：2026-11-24。

**OpenEMR 的定位**：如果目标是“在已有 EHR/患者门户上增加科普、问答和直播”，它比 Medplum 更重，但现成业务覆盖更广；如果只是做独立科普产品，完整 EHR 会带来不必要的部署、升级和 GPL 约束成本。[官方 README](https://github.com/openemr/openemr/blob/master/README.md)，访问：2026-11-24。

## 1. 开源/可复用实现核查

### 1.1 Medplum：医疗应用与智能体的基础平台

- **仓库与元数据（GitHub 官方 API，访问：2026-11-24）**
  - 仓库：[medplum/medplum](https://github.com/medplum/medplum)
  - Stars：**2,815**；Forks：1,035。
  - `updated_at`：**2026-11-23T20:50:36Z**；`pushed_at`：**2026-11-24T06:12:50Z**。
  - 许可证：**Apache-2.0**；语言字段：TypeScript；未归档。
  - 元数据来源：[GitHub REST API](https://api.github.com/repos/medplum/medplum)。
- **官方技术栈/能力**
  - 官方 README 将其定义为 healthcare developer platform，列出 Medplum Auth、FHIR-based API、Clinical Data Repository、SDK、App、Bots 和 React UI Component Library。[README](https://github.com/medplum/medplum/blob/main/README.md)，访问：2026-11-24。
  - 官方 README 的代码库技术说明是 full-stack TypeScript；Node.js 服务端、React 前端、PostgreSQL、Redis、Express。[README](https://github.com/medplum/medplum/blob/main/README.md)，访问：2026-11-24。
  - 官方文档导航覆盖患者应用、医生端临床工作流、Messages、Questionnaires、Bots、身份/权限、SMART Health Links 和自托管部署。[Medplum 官方文档](https://www.medplum.com/docs)，访问：2026-11-24。
- **部署难度：中高**
  - 官方项目提供本地开发和 Docker/Kubernetes/AWS/GCP/Azure 等自托管路径，说明基础设施路线完整；但生产环境至少要处理 PostgreSQL、Redis、对象/文件存储、身份、邮件、密钥、审计、备份、监控和升级。[自托管文档](https://www.medplum.com/docs/deploy)，访问：2026-11-24。
  - 评估：**中**适合先做受控原型；**高**适合承载真实患者数据，因为 FHIR 权限、多租户、审计和灾备必须由团队验证。
- **可直接复用**
  - FHIR 资源模型、API/SDK、SMART-on-FHIR/OAuth 身份能力、基础用户/项目权限、React 医疗 UI 组件。
  - 服务端 Bots 的触发、Webhook、定时任务和 FHIR 数据读写路径，可用来做“科普任务提醒”“问卷后自动反馈”“医生审核队列”等确定性流程。[Bots 官方文档](https://www.medplum.com/docs/bots)，访问：2026-11-24。
  - 作为“医生端 + 患者端 + 智能体工具调用”的数据边界：模型只通过受控 Bot/API 访问允许的 FHIR 资源。
- **需二次开发**
  - 医疗科普知识库、引用/版本/失效日期、适应证与禁忌证规则、模型路由、检索增强、答案模板、红线拒答和人工转接。
  - 患者端交互流程、内容管理、游戏化积分/徽章/排行榜、学习进度、直播间和实时聊天室。
  - 中国本地身份、支付、短信、电子签名、数据存储区域、日志留存和医院现有 HIS/EMR 对接。
- **主要风险**
  - **平台不等于医疗合规交付**：FHIR 和权限能力能降低工程风险，但不能证明具体科普答案、模型、流程或部署符合当地医疗监管。
  - **多租户和权限误配**是高后果风险；必须用真实角色矩阵、越权测试和审计演练验证，而不能只看文档。
  - Apache-2.0 允许较宽松复用，但依赖组件、云服务条款和医疗数据处理协议仍需单独核查。

### 1.2 OpenEMR：完整 EHR、患者门户与远程医疗的重型基座

- **仓库与元数据（GitHub 官方 API，访问：2026-11-24）**
  - 仓库：[openemr/openemr](https://github.com/openemr/openemr)
  - Stars：**5,623**；Forks：3,143。
  - `updated_at`：**2026-11-24T05:32:46Z**；`pushed_at`：**2026-11-24T06:06:01Z**。
  - 许可证：**GPL-3.0**；语言字段：PHP；未归档。
  - 元数据来源：[GitHub REST API](https://api.github.com/repos/openemr/openemr)。
- **官方技术栈/能力**
  - 官方 README 将其定义为开源 EHR 与 medical practice management，涵盖 EHR、排班、账单、国际化等；代码库构建路径包含 Composer、Node.js、npm。[README](https://github.com/openemr/openemr/blob/master/README.md)，访问：2026-11-24。
  - 官方 FHIR 文档声明提供 FHIR R4、US Core 8.0、SMART on FHIR v2.2.0、Bulk Data 等接口能力；这里应把“官方声明”与本地部署后的实际认证/配置效果区分开。[FHIR README](https://github.com/openemr/openemr/blob/master/FHIR_README.md)，访问：2026-11-24。
  - 官方患者门户架构文档显示，患者门户有独立患者会话、门户 JSON API、预约、文档、健康快照、Secure Messaging、账单和问卷等入口；同时明确指出门户并不直接使用公共 REST/FHIR 控制器，而是有自己的 `/portal/patient/api/*` 数据路径。[患者门户架构](https://github.com/openemr/openemr/blob/master/docs/architecture/portal.md)，访问：2026-11-24。
  - 官方仓库包含 Comlink Telehealth 模块，用于 OpenEMR 安装的 video conferencing。[模块 README](https://github.com/openemr/openemr/blob/master/interface/modules/custom_modules/oe-module-comlink-telehealth/Readme.md)，访问：2026-11-24。
- **部署难度：高**
  - 虽然官方提供 Docker 文档，但它是完整临床业务系统，升级、数据库、用户权限、患者门户、FHIR、审计、备份和第三方模块会显著增加运维面。[Docker README](https://github.com/openemr/openemr/blob/master/DOCKER_README.md)，访问：2026-11-24。
  - 评估：适合已有 EHR 运维能力的团队；不适合作为只做科普互动的轻量 MVP 起点。
- **可直接复用**
  - 患者门户登录/会话、预约、文档、消息、问卷入口；医生端 EHR、排班和患者上下文。
  - FHIR/SMART 接口可作为外部科普应用与 EHR 之间的标准化连接面；Comlink 模块可参考远程医疗房间的业务集成方式。
- **需二次开发**
  - 医疗科普智能体的知识库、答案审核、模型安全、游戏化教学与直播产品层。
  - 若将患者门户作为科普产品入口，需要重做信息架构、移动体验、内容运营和消息/通知策略。
  - 需要把门户既有内部 API 与公共 FHIR/REST API 的边界梳理清楚，避免把内部路径当成稳定集成契约。
- **主要风险**
  - **GPL-3.0** 会影响分发、修改和闭源集成的合规设计；商业化前必须由法务按实际交付方式确认。
  - 患者门户架构文档自己也指出其数据路径与公共 FHIR/REST 不一致；跨模块集成容易出现权限、数据一致性和升级兼容风险。
  - EHR 的功能复杂度会把项目重心从科普体验拖向临床系统运维；只有确有 EHR 替代/整合需求时才值得承受这一成本。

### 1.3 Rasa Open Source：对话流程编排参考，但存在生命周期风险

- **仓库与元数据（GitHub 官方 API，访问：2026-11-24）**
  - 仓库：[RasaHQ/rasa](https://github.com/RasaHQ/rasa)
  - Stars：**21,403**；Forks：4,910。
  - `updated_at`：**2026-11-23T19:50:24Z**；`pushed_at`：**2026-07-24T07:39:04Z**。
  - 许可证：**Apache-2.0**；语言字段：Python；未归档。
  - 元数据来源：[GitHub REST API](https://api.github.com/repos/RasaHQ/rasa)。
- **官方技术栈/能力**
  - 官方 README 定义它为 Python 生态的文本/语音对话机器学习框架，支持上下文对话、自定义 channel、forms/actions 等经典能力；开发安装使用 Poetry。[README](https://github.com/RasaHQ/rasa/blob/3.6.x/README.md)，访问：2026-11-24。
  - 官方仓库顶部明确标注 **Maintenance Mode**，并将未来方向指向 Hello Rasa 和 CALM；这比 Stars 更能代表新项目的维护判断。[README](https://github.com/RasaHQ/rasa/blob/3.6.x/README.md)，访问：2026-11-24。
  - 官方文档仍列出 Website、Telegram、Twilio、Slack、Microsoft Bot Framework、语音 channel、自定义 connector，以及 Tracker Store、Event Broker、Secrets Manager、HTTP API 等生产组件。[Rasa 官方文档](https://rasa.com/docs/rasa/)，访问：2026-11-24。
- **部署难度：中**
  - 单纯做规则/流程型问答可中等难度；真正生产化需训练/评测、Action Server、Tracker Store、模型存储、PII 管理、监控、CI/CD、人工接管和多渠道适配，难度上升到中高。
- **可直接复用**
  - 对话状态机、槽位收集、表单、确定性 action、渠道连接器、人工转接和会话事件模型。
  - 适合承载“症状分流前置问询”“科普课程导航”“直播前问题收集”等不直接替代诊疗的流程。
- **需二次开发**
  - 医学意图/实体、科普知识库、证据引用、答案审校、风险分级、危急症识别、人工接管 SLA、患者身份和医疗数据隔离。
  - 如果采用 Rasa 新路线，需要重新评估 CALM/Hello Rasa 的许可、托管、版本和迁移路径，不能只按经典 OSS 文档实施。
- **主要风险**
  - **维护模式是最强风险信号**：高 Stars 不代表未来版本和安全修复满足新项目要求。
  - 经典 NLU/对话策略容易把“对话流程可控”误认为“医学答案可信”；医疗事实仍需证据检索、规则约束和人工兜底。
  - Apache-2.0 解决的是代码许可，不解决模型、训练数据、第三方 channel 和患者信息合规。

### 1.4 H5P Core：交互式/游戏化健康教育内容层

- **仓库与元数据（GitHub 官方 API，访问：2026-11-24）**
  - 仓库：[h5p/h5p-php-library](https://github.com/h5p/h5p-php-library)
  - Stars：**156**；Forks：148。
  - `updated_at`：**2026-11-17T01:57:18Z**；`pushed_at`：**2026-11-23T13:12:24Z**。
  - 许可证：**GPL-3.0**；语言字段：PHP；未归档。
  - 元数据来源：[GitHub REST API](https://api.github.com/repos/h5p/h5p-php-library)。
- **官方技术栈/能力**
  - 官方 README.txt 说明该仓库是通用 H5P library，不绑定特定框架；宿主必须实现 `H5PFrameworkInterface`，提供上传表单，并把 H5P 包放入临时目录。[README.txt](https://github.com/h5p/h5p-php-library/blob/master/README.txt)，访问：2026-11-24。
  - `composer.json` 将其声明为 PHP library，最低 PHP 版本为 7.0，许可证为 GPL-3.0。[composer.json](https://github.com/h5p/h5p-php-library/blob/master/composer.json)，访问：2026-11-24。
  - H5P 官方内容目录列出 Interactive Video、Course Presentation、Branching Scenario、Quiz、Game Map、Flashcards、Memory Game、Questionnaire 等，并支持接入 WordPress、Moodle、Drupal 或通过 LTI 接入其他 VLE。[官方内容目录](https://h5p.org/content-types-and-applications)，访问：2026-11-24。
- **部署难度：中**
  - 若已有 Moodle/WordPress/Drupal，接入难度较低；若要在自研患者端独立承载，需要实现宿主接口、包管理、静态资源、安全隔离、成绩/进度存储、权限和内容发布流程，难度为中高。
- **可直接复用**
  - 交互式视频、分支情景、选择题/测验、拖拽、记忆卡、时间线和游戏地图等健康教育交互范式。
  - 内容包和内容类型机制可用于把医学知识拆成版本化课程，形成“内容编辑—发布—学习—结果”的闭环。
- **需二次开发**
  - 医疗内容审核、来源引用、地区/人群适配、无障碍、题目难度、学习效果指标和与患者身份/FHIR 资源的绑定。
  - 需要自建或选择宿主平台；不能把 `h5p-php-library` 当作独立的内容管理后台或患者应用。
- **主要风险**
  - **许可证边界**：Core 为 GPL-3.0，官方组织下不同 content type 仓库的许可证可能不同；分发整合包前必须逐仓库核查，不能只看 H5P 品牌或单个组件。
  - H5P 强在内容交互，不保证医学事实正确；每个课程仍需专业审核、版本失效管理和事件升级路径。
  - 积分、徽章、排行榜可能诱导不当健康行为；医疗场景应优先采用完成度、理解度和安全行为反馈，而非单纯竞争排名。

### 1.5 LiveKit：实时音视频、直播和 AI 参与者底座

- **仓库与元数据（GitHub 官方 API，访问：2026-11-24）**
  - 仓库：[livekit/livekit](https://github.com/livekit/livekit)
  - Stars：**22,359**；Forks：2,595。
  - `updated_at`：**2026-11-24T05:55:42Z**；`pushed_at`：**2026-11-23T21:24:10Z**。
  - 许可证：**Apache-2.0**；语言字段：Go；未归档。
  - 元数据来源：[GitHub REST API](https://api.github.com/repos/livekit/livekit)。
- **官方技术栈/能力**
  - 官方 README 将仓库定义为 Go 编写的可扩展 WebRTC SFU；负责在人、设备、AI 模型之间转发实时音频、视频和数据。[README](https://github.com/livekit/livekit/blob/master/README.md)，访问：2026-11-24。
  - 官方列出 SDK、JWT 鉴权、UDP/TCP/TURN、moderation API、端到端加密、data tracks、SIP、webhooks、分布式多地域，以及单二进制、Docker、Kubernetes 部署。[README](https://github.com/livekit/livekit/blob/master/README.md)，访问：2026-11-24。
  - 官方自托管文档要求生产部署准备域名、可信 SSL、负载均衡/反向代理，并根据网络情况配置 TURN；这决定了真实直播不是“启动一个容器”就结束。[部署文档](https://docs.livekit.io/transport/self-hosting/deployment/)，访问：2026-11-24。
- **部署难度：中高**
  - 本地 PoC 较容易；生产直播需要公网网络、域名/证书、TURN、负载均衡、带宽、录制/转码、监控、滥用防护、容量规划和多地域策略，难度中高。
- **可直接复用**
  - 直播间/房间、主播与观众身份、音视频发布订阅、低延迟数据通道、屏幕共享、实时 AI agent 参与、录制/导入导出等实时能力。
  - 可把医生、患者、主持人、审核员和 AI 助手建模为不同 participant/权限，使用 JWT 和房间权限控制。
- **需二次开发**
  - 医生资质与机构认证、患者实名/匿名策略、病例/影像脱敏、评论审核、敏感词/风险词识别、转人工、录制留存、删除请求、内容版权、直播回放审核。
  - “直播科普”与“在线诊疗”必须拆分产品和权限；后者涉及更高等级的身份、知情同意、医疗记录和合规审计。
- **主要风险**
  - WebRTC 的传输安全不等于医疗数据全生命周期合规；录制、截图、聊天、AI 转写和第三方云服务仍可能泄露敏感信息。
  - 低延迟会压缩审核时间；必须预先定义主播端、AI 端和观众端的实时拦截/降级策略。
  - Apache-2.0 允许复用，但自托管所需的网络和运维能力可能高于团队预估；LiveKit Cloud 与自托管的成本/数据边界需单独决策。

## 2. 五个维度横向评估

| 实现 | Stars（访问日快照） | 最近更新时间（GitHub API） | 许可证 | 技术栈 | 部署难度 | 最适合的复用位置 |
|---|---:|---|---|---|---|---|
| Medplum | 2,815 | updated 2026-11-23；pushed 2026-11-24 | Apache-2.0 | TypeScript / Node / React / PostgreSQL / Redis / Express / FHIR | 中高 | 医疗数据、身份权限、医生/患者应用、Bot |
| OpenEMR | 5,623 | updated 2026-11-24；pushed 2026-11-24 | GPL-3.0 | PHP / Composer / Node.js / npm / MySQL 系生态 / FHIR / SMART | 高 | EHR、患者门户、排班、消息、远程医疗集成 |
| Rasa OSS | 21,403 | updated 2026-11-23；pushed 2026-07-24 | Apache-2.0 | Python / Poetry / NLU / dialogue policies / Action Server / connectors | 中（生产中高） | 对话流程、槽位、表单、人工转接 |
| H5P Core | 156 | updated 2026-11-17；pushed 2026-11-23 | GPL-3.0 | PHP / Composer；content types 多为 JS 组件 | 中 | 游戏化课程、互动视频、分支情景、测验 |
| LiveKit | 22,359 | updated 2026-11-24；pushed 2026-11-23 | Apache-2.0 | Go / Pion WebRTC / SDK / Docker / Kubernetes | 中高 | 直播、实时互动、AI 参与者、实时数据 |

**比较时最容易误判的地方**：Stars 只能说明关注度，不能证明医疗安全、商业 SLA 或本地化能力；OpenEMR/Rasa 的 Stars 高于 Medplum/H5P Core，但这并不意味着它们更适合一个独立的医疗科普产品。

## 3. 面向医生、患者、直播的官方产品能力参考

以下不是“已完成的竞品功能清单”，而是可以用来拆需求和验收边界的官方能力参考。

| 面向对象 | 官方产品/能力 | 可借鉴的产品能力 | 不能直接推断的部分 |
|---|---|---|---|
| 医生/医疗应用开发 | **Medplum**：FHIR API、SMART/OAuth、React UI、Bots、临床工作流 | 医生端可把科普审稿、问卷、患者分层、随访提醒和内容发布建成受权限控制的工作流 | Medplum 不替你提供医学知识审核、模型评测或中国本地医疗合规 |
| 医生/患者 | **OpenEMR Patient Portal + FHIR/SMART** | 患者独立登录/会话、预约、文档、健康信息、Secure Messaging、问卷；外部应用通过 FHIR/SMART 连接 | 官方架构明确门户有自己的内部数据路径；不能把门户内部 API 当长期稳定的公共集成协议 |
| 患者/健康教育 | **H5P**：Interactive Video、Branching Scenario、Quiz、Game Map、LTI | 可将一场科普直播剪辑成互动视频，将复杂健康决策做成分支情景，把理解度做成测验而非只看播放量 | 互动形式不等于医学有效性；题库和反馈必须经过专业审核和效果评估 |
| 医生/患者/主持人 | **LiveKit**：WebRTC rooms、JWT、moderation API、E2EE、data tracks、webhooks | 可实现低延迟直播、医生与患者连麦、AI 助手入场、实时投票/问卷/字幕/设备数据 | 不提供医生资质、知情同意、病例脱敏、评论政策和医疗责任体系 |
| 对话互动 | **Rasa**：渠道连接器、forms、custom actions、tracker/event、human handoff | 可把“问答—澄清—收集信息—转人工”做成可观测的流程，而不是只有一个自由生成文本框 | OSS 官方仓库处于 maintenance mode；新项目需先确认采用经典 OSS、CALM/新平台还是换用其他编排层 |

来源：

- [Medplum 官方文档](https://www.medplum.com/docs)；[Medplum GitHub README](https://github.com/medplum/medplum/blob/main/README.md)；访问：2026-11-24。
- [OpenEMR 患者门户架构](https://github.com/openemr/openemr/blob/master/docs/architecture/portal.md)；[OpenEMR FHIR README](https://github.com/openemr/openemr/blob/master/FHIR_README.md)；访问：2026-11-24。
- [H5P 官方内容目录](https://h5p.org/content-types-and-applications)；访问：2026-11-24。
- [LiveKit GitHub README](https://github.com/livekit/livekit/blob/master/README.md)；[LiveKit 自托管部署](https://docs.livekit.io/transport/self-hosting/deployment/)；访问：2026-11-24。
- [Rasa 官方文档](https://rasa.com/docs/rasa/)；[Rasa Open Source README](https://github.com/RasaHQ/rasa/blob/3.6.x/README.md)；访问：2026-11-24。

## 4. 面向落地的组合方案

### 方案 A：独立医疗科普产品（推荐起点）

`Medplum（身份/FHIR/权限） + H5P（课程与互动） + LiveKit（直播） + 受控对话编排`

- 适合：医院/医生团队已有内容与审核能力，想做患者端科普、问卷、互动课程和直播。
- 直接收益：各层边界清楚，FHIR 负责医疗数据，H5P 负责内容交互，LiveKit 负责实时传输，智能体只通过工具访问允许的数据。
- 必须自建：知识库与引用、医疗内容审核、模型安全、内容生命周期、直播审核、患者身份与同意、指标体系。

### 方案 B：EHR 增强型

`OpenEMR（EHR/门户/临床工作流） + H5P + LiveKit 或其现有远程医疗模块`

- 适合：目标是已有 OpenEMR 机构的患者教育、随访、消息和远程医疗增强。
- 直接收益：患者和医生已有临床上下文，不必重新造完整 EHR。
- 必须自建：科普产品体验、AI 编排、跨门户/FHIR 数据一致性、升级兼容和 GPL 合规。

### 方案 C：对话优先型

`Medplum/OpenEMR（医疗事实与权限） + 对话编排层 + H5P/LiveKit`

- 适合：核心价值是“问答—分流—课程—人工接管”，而不是 EHR 本身。
- Rasa 只能作为已验证的流程编排参考；如果选择它，先做版本维护、漏洞修复、渠道和新路线迁移评估。
- 不建议：把通用大模型直接接到患者病历、诊断建议或处方决策上，且没有引用、规则、权限、日志和人工兜底。

## 5. 可推翻结论的条件与反方证据

当前前三推荐是**按“跨四个方向的可组合性 + 医疗边界 + 生命周期风险”**做的，不是按 Stars 排名。以下证据出现时，应重新排序：

1. **如果团队已有成熟 EHR 和 OpenEMR 运维能力**，并且需求是患者门户/预约/消息/远程医疗增强，OpenEMR 可能应排在 Medplum 前面；反方证据是“现有机构数据和工作流已经深度绑定 OpenEMR，迁移到 Medplum 的成本高于二次开发”。
2. **如果核心场景是同步课堂而非课程内容**，LiveKit 的部署和审核成本可能让现成商业直播/会议服务更优；反方证据是“已有合规的视频云、录制、审核和 CDN，且不需要自托管”。
3. **如果目标是 LMS 内的课程而不是自研患者端**，H5P 的 GPL/宿主接口成本可能让 Moodle/WordPress 原生集成或商业内容工具更优；反方证据是“机构已有 H5P 宿主，且无需闭源分发 Core”。
4. **如果 Rasa 官方给出新的长期支持路线、明确安全维护周期和可接受的迁移承诺**，它可重新成为对话层候选；反方证据必须来自官方版本/维护政策，而不是社区 Stars 或第三方教程。
5. **如果本地监管要求或医院采购规则排除云端/开源组件**，上述组合的技术优势都不足以抵消采购与合规约束；此时应以数据驻留、等保/审计、医疗器械边界、供应商责任和合同 SLA 重新筛选。

## 6. 建议的下一步验证问题

- 目标首先是**健康教育**、**患者服务/随访**、还是**在线诊疗**？三者的医疗责任和合规边界不同，不能只用“医疗科普”统称。
- 患者数据是否要进入现有 HIS/EMR/EHR？若要进入，优先确认 FHIR/SMART、主索引、授权和审计，而不是先选聊天框架。
- 直播是否允许连麦、录制、AI 转写和回放？这些选项会直接改变同意、留存、审核和带宽设计。
- 游戏化指标是完成率、理解度、行为改变，还是活跃/留存？医疗教育不应默认用排行榜或时长作为成功指标。

