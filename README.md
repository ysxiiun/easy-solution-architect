# easy-solution-architect

`easy-solution-architect` 是一个面向 Agent 的架构设计 skill，用于基于当前项目代码、`.easy-coding` 过程文档和用户本轮需求，产出可直接指导开发实施的技术方案，并在用户确认后写入 `.easy-coding/spec/Architect-Spec.md`。

## 触发方式

仅支持显式加载：

- `使用 $easy-solution-architect ...`
- `加载 easy-solution-architect ...`
- `使用 Easy Solution Architect skill ...`

普通技术方案、架构设计或 Architect-Spec 描述不会自动加载本 skill，除非用户同时显式点名 Easy Solution Architect。

它适合这样一类场景：产品和交互方向已经初步明确，但技术栈、模块边界、数据模型、API 设计、状态流转和异步机制还没有被系统化收敛。这时它不会一上来就直接写一版“像样但可能不准”的 Spec，而是先做证据分析、识别冲突与缺口，再推动关键决策收敛。

## Skill 介绍

这个 skill 的默认工作方式是：

- 先静默分析项目代码、目录结构与 `.easy-coding` 文档
- 能从现有材料确认的信息，优先自行消化，不反问用户
- 只围绕冲突项、缺失项和高影响决策发问
- 追问统一使用 `A/B/C/D` 选项式表达，方便用户直接回复字母
- 在关键问题收敛前，不提前输出接近完整 Spec 的中间稿
- 先给一版架构方案摘要，等用户确认后再写入 `Architect-Spec.md`

它的核心目标不是“尽快生成文档”，而是“尽可能用现有证据把方案做对，再生成真正可实施的架构 Spec”。

## 适用场景

- 为新需求补充或新建 `.easy-coding/spec/Architect-Spec.md`
- 基于 `Product-Spec.md`、`UI-Spec.md`、原型文档和项目代码输出技术方案
- 分析现有项目的技术栈、目录分层、模块职责和数据模型
- 为后续开发补充 API 设计、状态机、事件流和异步处理方案
- 在已有历史设计文档的基础上，对架构方案做增量更新

## 不适用场景

- 仅做产品定义、商业分析或市场判断
- 直接进入编码实现，而不需要前置技术方案
- 只想要非常粗粒度的想法整理，不需要实施级别的架构 Spec
- 在缺乏基本需求背景的情况下，要求立即输出完整技术设计

## 工作方式

### 1. 先发现输入，再判断缺口

skill 会优先读取以下信息：

1. 当前项目代码与目录结构
2. `.easy-coding/spec/Architect-Spec.md`
3. `.easy-coding/spec/Product-Spec.md`
4. `.easy-coding/spec/UI-Spec.md`
5. `.easy-coding/prototype/Easy-UI-Prototype.md`
6. `.easy-coding/prototype/index.html`、同目录页面 HTML、`assets/` 与 `images/` 中的原型产物索引
7. 用户当前提示词

原型输入只认 `.easy-coding/prototype/` 当前规范路径；如果该路径或 `.easy-coding/prototype/Easy-UI-Prototype.md` 缺失，不会搜索旧路径或项目根目录下的原型文件，而是按“无原型输入”继续分析。

如果 `.easy-coding` 缺失，它不会立刻中断，而是继续基于代码现状分析，并只对真正会阻断技术决策的问题发问。

### 2. 先做问题规划，再多轮追问

在正式提问前，skill 会先在内部生成一份问题规划，用来区分：

- 哪些信息可以从代码和文档中推断
- 哪些问题会影响技术路线或文档正确性
- 哪些问题必须优先确认

正式追问时遵循以下约束：

- 每轮最多问 3 个问题
- 每个问题都使用 `A/B/C/D` 选项
- 问题只聚焦关键阻断项，不做泛泛盘问
- 上一轮未回答完前，不开启下一轮新问题

### 3. 先摘要确认，再落文档

只有当以下条件都满足时，才会进入正式写文档阶段：

- 所有阻断型问题已关闭
- 不再存在会影响技术路线、数据模型、接口形态、状态机或异步机制的关键疑虑
- 用户已确认架构方案摘要

摘要至少会覆盖：

- 需求理解
- 主要输入依据
- 已发现的冲突或风险
- 技术栈判断或建议
- 目录分层与模块划分
- 数据模型与持久化方案
- API、状态机、事件流与异步设计结论

### 4. 写完后做冲突复核

在写入 `.easy-coding/spec/Architect-Spec.md` 后，skill 会重新对照：

- `Product-Spec.md`
- `UI-Spec.md`
- `.easy-coding/prototype/Easy-UI-Prototype.md`
- 相关 HTML 原型文件
- 图片模式下的页面索引、图片文件名和提示词摘要

复核是否存在明显冲突，例如：

- 页面流程和模块设计冲突
- 核心对象与字段定义冲突
- 状态流转与接口设计冲突
- 原型交互与异步机制冲突

如果没有明显冲突，正常交付；如果发现关键冲突，则应明确指出冲突点、来源和当前采用方案。

## 输出内容

默认只生成或更新一个文件：

- `.easy-coding/spec/Architect-Spec.md`

固定约束如下：

- 默认语言为中文
- 默认文档粒度为实施级技术 Spec
- 若已有 `Architect-Spec.md`，优先增量更新并尽量保留原结构
- `Product-Spec.md`、`UI-Spec.md`、原型说明、HTML 原型、AI 图片原型与资源索引默认只读

## 文档骨架

`Architect-Spec.md` 使用固定骨架，章节即使不适用也需要保留，并明确填写“无”。

当前模板位于：

- [references/architect-spec-template.md](./references/architect-spec-template.md)

固定章节包括：

1. 需求背景与目标
2. 输入依据与冲突说明
3. 系统基础技术栈
4. 架构分层与目录职责
5. 数据模型设计
6. 核心模块设计
7. API 设计
8. 状态机设计（如需要）
9. 事件流与异步设计
10. 关键流程与交互链路
11. 非功能性设计
12. 实施建议与风险

其中 `API 设计`、`状态机设计（如需要）`、`事件流与异步设计` 是常规模板章节，没有内容时也必须明确写“无”。

## 仓库内容

- [SKILL.md](./SKILL.md)：Skill 的正式行为约束与工作流定义
- [agents/openai.yaml](./agents/openai.yaml)：展示名称、简介和默认提示词配置
- [references/architect-spec-template.md](./references/architect-spec-template.md)：`Architect-Spec.md` 固定骨架与章节说明

## 使用示例

- “使用 `easy-solution-architect`，帮我基于当前仓库和已有 Product/UI Spec 补一版技术方案。”
- “先分析现有代码结构，再帮我整理成 `.easy-coding/spec/Architect-Spec.md`。”
- “我已经有原型和 UI Spec 了，帮我补齐 API、状态机和异步设计。”
- “先不要急着写文档，先把会影响架构路线的关键问题问清楚。”
