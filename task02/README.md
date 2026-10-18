# Task02｜从零开始运行 DSH：Workspace、模型与四种 Preset

> Datawhale《DeepSeek Harness 技术入门》学习小队：鲸构万象  
> 课程依据：《DeepSeek Harness 技术入门与架构原理》第2章（教材目录：P11–P19）  
> 笔记时间：2026-10-18  
> **状态：导学笔记与实机实验方案已整理；课程正文尚待本人逐页核对；本轮实机实验尚未执行；Datawhale 平台尚未提交。**

## 一、Task02 学习目标

本章不只是「把 DSH 启动起来」，而是用实证解释：

1. **Workspace**：Agent 的读写活动发生在哪里？工作区是否等于安全沙箱？
2. **模型配置**：Model、Provider、API、凭据与运行时有什么关系？
3. **四种 Preset**：Standard、PTC、Minimal、Creative 为什么不是四个独立 Agent？
4. **Composition 差分**：插件组合改变之后，工具、指令及执行能力怎样变化？
5. **执行轨迹对比**：如何在同一输入、模型、工作区条件下观察不同 Preset？
6. **Minimal 基准测试**：精简工具表面能帮助控制哪些变量？又有什么局限？

> 核心认知：**Workspace 是任务的操作环境；Profile 是进程的组装方案；Preset 是会话中的 Agent 能力组合。三者不是同一个东西。**

## 二、教材第2章知识点

### 2.1 环境准备

- 官方仓库的 Web UI 快速启动示例：`npx @deepseek-ai/dsh web`。
- 通常会启动本地网页服务（README 默认 `http://127.0.0.1:3080`），但若你使用桌面端或定制发行版，应以实际软件中的入口及版本为准。
- 从源码开发的系统要求与普通安装不同：当前官方开发指南说明 Node.js 22.19+ 或受支持的 24+、配套 pnpm 与 Git 等条件。无需为已有桌面端重复安装。
- DSH 处于 Developer Preview，必须注意版本兼容，特别是第三方插件和实验性配置。

来源：[官方中文 README](https://github.com/deepseek-ai/deepseek-harness/blob/master/README.zh.md)；[官方开发指南](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/development.zh.md)。

### 2.2 Workspace 是 Agent 的“现实世界”

**Workspace** 是 Agent 本轮任务所面向的工作目录，关系到可读取的文件、工具执行时的工作路径及输出落点。官方 Web UI 文档说明：

- 在 Web UI 中选好 Workspace 以后才可正常开始新会话；
- Agent 可能读取、编辑文件，运行命令，甚至委派任务；
- 操作需要不需要审批，取决于激活的**权限策略**，不能仅凭自然语言提示保证安全。

**重要边界**：选择 Workspace 不等于给 Agent 配置了完善的沙箱；是否能访问工作区之外的资源，必须结合工具、权限、沙箱配置来判断。因此建议使用独立的虚构样本目录进行课程实验。

来源：[官方 Web UI 使用指南](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/user/guide/index.zh.md)。

### 2.3 配置模型

应区分四件事：

| 概念 | 含义 |
| --- | --- |
| Provider | 提供模型服务的路由或供应商 |
| Model | 本次实际选择的模型 id |
| Credential/API Key | 模型接口访问凭据（不要写入公开笔记） |
| Harness | 负责请求模型、执行工具、管理上下文与运行过程的外部机制 |

目前官方 Web UI 的路径是「设置 → 模型」；官方说明支持内置提供商、自定义模型 API 等配置。保存的密钥采用引用与脱敏方式，不应复制到截图或仓库。

**版本提示**：2026年8月的社区白皮书中部分 `settings.yaml` 示例，与近期官方文档的 `cordis.patch.yml` 与凭据存储机制不完全一致。实际修改之前必须以当前安装版本的官方文档为准。

来源：[官方模型配置指南](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/user/guide/providers.zh.md)。

### 2.4 四套 Agent Preset

教材使用：**Standard、PTC、Minimal、Creative**。

| Preset | 核心特点（导学层面） | 建议观察点 |
| --- | --- | --- |
| Standard 标准 | 面向一般任务的较完整能力组合 | 能看到哪些工具、权限和计划能力？ |
| PTC 程序化工具调用 | 通过代码组织多步工具调用 | 工具呈现和执行轨迹与 Standard 有何不同？ |
| Minimal 极简 | 尽量减小工具和运行时附加能力 | 能否完成任务；有哪些能力缺失？ |
| Creative 创造 | 偏向运行时检查、配置和插件试验 | 是否暴露额外扩展或调试能力？ |

**Preset ≠ Profile**：

- **Plugin**：提供具体功能的模块；
- **Bundle**：可分发的插件与配置组合；
- **Profile**：启动某个 `dsh` 进程时由多个 Bundle 与 Patch 叠加的装配方案（如 web、headless、sdk、acp）；
- **Preset**：某个 Agent 会话采用的能力组合。

来源：[官方架构说明](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/architecture.zh.md)；教材目录第2章、第3章。

### 2.4.2 Composition 的“差分”

不要把“模式切换”理解成换了大模型。观察的重点是：

- **同一个模型**下，不同 Preset 注入了哪些工具、系统指令及运行能力；
- 哪些能力依赖同一个宿主插件，哪些属于会话级组件；
- 添加插件或改变配置之后，是否影响可见工具、调用记录与最终结果。

必要时，可在**支持 CLI 的实验环境**执行只读命令：

```bash
dsh --profile web --dump-config
```

此命令用于打印实际装配树；如果你只安装了桌面版，没有独立 `dsh` 命令，就不强求执行。**不要直接修改正式 Profile，也不要向公开仓库上传可能包含路径/凭据的完整配置。**

来源：[官方架构文档](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/architecture.zh.md)。

### 2.4.3 比较同一任务的执行轨迹

对照实验至少固定：

1. **同一模型 / Provider**；
2. **同一份虚构素材、同一工作区权限**；
3. **同一任务提示词**；
4. 分别使用可用的 Preset，记录工具调用、任务结果、错误与审批。

仅切换 Preset 后的单次观察，不足以构成严谨 benchmark；但足以帮助本章理解“能力组合确实会影响 Agent 行为”。

### 2.5 为什么官方 Benchmark 可能使用 Minimal？

**分析而非替官方作结论**：Minimal 刻意缩小工具表面与系统附加能力，可能有助于聚焦“模型在特定基础工具环境中的表现”，减少完整产品复杂度带来的额外变量。

需要注意：

- Minimal 的成绩**不能直接等同**于 Standard 或桌面产品的用户体验；
- 比较模型表现时，应固定任务、上下文、工具、模型参数和评测规则；
- Benchmark 测的是“模型 + 特定 Harness 配置”，不是脱离环境的纯模型能力。

具体官方 Benchmark 设置、指标和原始证据需在对应 benchmark 文档进一步核实。

## 三、针对我的现有 DSH 环境：推荐实验（**尚未执行**）

不改变已经正式投入使用的 Personal AI Worktable，也不使用真实客户资料。

### 3.1 准备样本 Workspace

创建独立测试文件夹（**名称仅为建议**）：

```text
dsh-task02-lab/
├── project-brief.md
└── tasks.md
```

`project-brief.md`：

```markdown
# 虚构文旅项目 A

项目定位：城市滨水公共活动空间。
资料：拟设置步行环线、公共休憩区和临时展览区。
尚未提供：投资预算、场地面积、建设时间。
```

`tasks.md`：

```markdown
# 研究要求
请明确区分已提供事实与尚待确认的信息；不编造预算和规模数据。
```

### 3.2 对照提示词

> 请阅读当前工作区的 project-brief.md 与 tasks.md，仅根据文件内容完成：  
> ① 摘录3条已知事实；② 列出3项待核实问题；③ 说明本次任务实际读取了哪些文件。  
> **只读操作，不要写入、修改、删除文件，也不要访问其他目录。**  
> 如果遇到权限审批，请先暂停并报告。不要自行编造缺失信息。

**注意**：“只读操作”是任务指令，不是实际的安全防护；仍需将环境权限设置为保守模式。

### 3.3 实验记录表（由本人执行后填写）

| 观察项 | Standard | Minimal | PTC（如可用） | Creative（如可用） |
| --- | --- | --- | --- | --- |
| 版本与模型 | 待填 | 待填 | 待填 | 待填 |
| 任务是否完成 | 待测 | 待测 | 待测 | 待测 |
| 使用的工具 | 待测 | 待测 | 待测 | 待测 |
| 关键执行步骤 | 待测 | 待测 | 待测 | 待测 |
| 有无错误或审批 | 待测 | 待测 | 待测 | 待测 |
| 输出是否严格依据素材 | 待测 | 待测 | 待测 | 待测 |

优先做 Standard 和 Minimal 两组，另外两组不必为了作业强行启用高风险能力。保存不含密钥、个人路径或真实项目数据的截图作为打卡附件。

## 四、学习心得（基于既有经历，非本轮实验结论）

之前我已经使用 DeepSeek Harness 配置过模型、工作区，并探索过 ACP 集成，主要精力在于让软件正常运行、完成任务。本章让我重新理解这些操作背后的层次：

- **Workspace** 不只是打开一个文件夹，它规定了实际工作对象；还要通过权限和沙箱控制风险；
- **Profile** 决定应用启动时怎样组装各类功能；
- **Preset** 决定某次会话的 Agent 如何使用这些能力，而不是改变模型本身；
- 评估 Agent 时不应该只看最终回答，还要看真实文件、工具轨迹、权限和可重现性。

对于我正在实践的个人 AI 工作台，这种分层思维比继续增加工具数量更重要。

## 五、Task02 打卡简述（按真实学习情况提交）

**主题**：从零开始运行 DSH：Workspace、模型与四种 Preset。

**学习要点**：理解了 Workspace、Profile、Preset 的区别；学习了模型与 Provider 的配置关系，认识到四套 Agent Preset 对应不同的能力组合；了解为什么要观察 Composition 差分、执行轨迹以及 Minimal Benchmark 的适用边界。

**实践说明**：已有 DSH 部署、模型配置和工作区使用经验；本轮新的 Standard/Minimal 对照实验**尚待在本人电脑上执行**，不将拟做的实验写为已完成成果。

**后续计划**：在隔离的虚构资料目录内进行对照实验，记录实际工具调用及执行结果，再更新本笔记。

## 六、理解检查

- [ ] 用自己的话解释 Workspace、Profile、Preset 之间的区别。
- [ ] 为什么指定 Workspace 不等于获得沙箱保护？
- [ ] 为什么同一个模型在不同 Preset 下可能产生不同的工具调用轨迹？
- [ ] 为什么 Minimal 上的 Benchmark 表现不能直接推断 Standard 的体验？
- [ ] 在个人 AI 工作台中，如何避免实验插件或配置影响正式数据？

## 七、参考资料与边界

- [人民邮电出版社：教材介绍和目录](https://detail.youzan.com/show/goods?alias=2xmiupyhq6gup98&from_source=gbox_seo)
- [Datawhale 官方课程](https://www.datawhale.cn/learn/summary/381)
- [DeepSeek Harness README（官方中文）](https://github.com/deepseek-ai/deepseek-harness/blob/master/README.zh.md)
- [使用 Web UI（官方中文）](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/user/guide/index.zh.md)
- [模型配置（官方中文）](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/user/guide/providers.zh.md)
- [架构与 Profile / Bundle（官方中文）](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/architecture.zh.md)

> **边界**：此文是基于教材目录、官方当前文档和个人已有实践形成的学习导读；**不是对教材 P11–P19 逐页摘录**。Datawhale Task02 的官方截止时间、是否必须提交特定实验与平台表单尚待助教通知核对。本次没有访问或修改用户本机 DSH 软件、配置与工作区，也没有提交 Datawhale 平台打卡。
