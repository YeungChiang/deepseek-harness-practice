# DeepSeek Harness 技术入门｜Datawhale 组队学习

> 我的 DeepSeek Harness 学习笔记、任务打卡和后续实践记录  
> 学习小队：**鲸构万象**  
> 学习内容：Datawhale《DeepSeek Harness 技术入门》（基于《DeepSeek Harness 技术入门与架构原理》）

## 学习目标

从“**会使用 DeepSeek Harness**”进一步走向“**理解 Agent Runtime 的组织机制**”：

- 理解 Model、Agent、Harness 的定位与边界；
- 理解 **Everything Is a Plugin**（一切皆插件）与 Cordis；
- 认识模型适配、工具、会话、执行循环和 Preset 的组织方式；
- 将可复用的设计思想应用到个人 AI 工作台和专业 AI 工作流中。

## 课程进度

本期组队学习范围为导读及前三章，按官方任务安排推进。

| Task | 主题 | 学习记录 | 状态 |
| --- | --- | --- | --- |
| **Task01** | 导读 + 第 1 章：建立坐标——为什么需要 Harness？ | [查看学习笔记](./task01/README.md) | **笔记已整理；教材逐页核对待完成** |
| **Task02** | 第 2 章：从零开始运行 DSH | [查看学习笔记和实验方案](./task02/README.md) | **笔记已整理；实机对照与平台打卡待完成** |
| **Task03** | 第 3 章：Preset、Scope、Host Plane / Agent Plane | [查看学习笔记、分层图与自测](./task03/README.md) | **已读教材；笔记整理完成，平台打卡待提交** |

> 此表记录 **GitHub 笔记整理进度**，不代表 Datawhale 平台已经完成打卡。

## 仓库结构

```text
deepseek-harness-practice/
├── README.md           # 课程说明、学习索引与进度
└── task01/
    └── README.md       # Task01 学习笔记与打卡摘要
├── task02/
│   └── README.md       # Task02 学习笔记与待执行的对照实验
└── task03/
    └── README.md       # Task03 Scope、Preset、分层图与自测
```

后续每个 Task 延续 `taskXX/README.md` 的结构；完成学习后再逐项添加，不预建空目录。

## 学习记录原则

1. **先理解，后总结**：记录概念、适用边界和自己实际验证过的内容。
2. **区分材料来源**：教材、官方文档、个人理解和推断分别标明。
3. **不虚构实验**：只有实际运行后，才记录命令、日志与结果。
4. **不公开敏感数据**：API Key、`.env`、个人文件、工作区私密配置及客户资料不上传。

## 参考资料

- [Datawhale 官方课程](https://www.datawhale.cn/learn/summary/381)
- [DeepSeek Harness 官方介绍](https://deepseek.com/harness/)
- [DeepSeek Harness 开源项目](https://github.com/deepseek-ai/deepseek-harness)
- [DSH 官方架构文档](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/architecture.md)

---

*本仓库为个人学习记录，与 Datawhale 或 DeepSeek 官方项目无隶属关系。*
