---
name: write-proposal
description: 科研申报书全流程撰写编排skill。当用户要求撰写申报书、申请书、项目申报、开题报告、proposal writing，或提到"帮我写申报书""从零开始写申报书""编排申报书各部分"等需求时触发。此skill不直接撰写内容，而是编排9个section-level skill的执行顺序、管理依赖关系、协调迭代修订，是申报书撰写的总指挥。即使用户只是说"开始写申报书"而没有指定具体section，也应使用此skill来规划整体流程。当9个子skill中的任何一个完成撰写后，也会回调本skill来更新状态和日志。
---

# 科研申报书全流程撰写编排

## 这个skill做什么

编排科研申报书各部分的撰写顺序，管理section之间的依赖关系，协调迭代修订。不直接撰写内容，而是作为总指挥调度以下9个section-level skill：

| 序号 | Skill名称 | 输出文件 | 对应section |
|------|----------|---------|------------|
| S1 | `write-research-purpose` | `1-研究目的与意义.md` | 立项依据-研究目的与意义 |
| S2 | `write-literature-review` | `2-国内外研究现状.md` | 立项依据-国内外研究现状分析和发展趋势 |
| S3 | `write-application-prospects` | `3-应用前景与学术价值.md` | 立项依据-应用前景和学术价值+行业企业实际问题 |
| S4 | `write-references` | `4-参考文献.md` | 立项依据-参考文献（文献调研） |
| S5 | `write-research-foundation` | `5-研究基础与条件.md` | 现有研究基础、条件、手段以及指导教师情况 |
| S6 | `write-research-objectives` | `6-研究目标与内容.md` | 研究方案-研究目标、研究内容和拟解决的关键问题 |
| S7 | `write-research-methods` | `7-研究方法.md` | 研究方案-拟采取的研究方法 |
| S8 | `write-feasibility` | `8-可行性分析.md` | 研究方案-可行性分析 |
| S9 | `write-innovation-plan` | `9-创新点与计划.md` | 研究方案-特色创新点+计划+预期成果 |

另有 `write-key-problems` 可与 S6 协同使用，提供关键问题部分的深度论证。

## 两种调用入口

本skill有两种被调用的方式，行为不同：

### 入口A：用户直接调用

用户通过对话触发（如"开始写申报书""继续写申报书"等）。

**执行流程**：
1. 读取 `<output_path>/proposal-state.json`，如不存在则进入初始化流程
2. 读取 `<output_path>/proposal-log.md`，如不存在则创建
3. 向用户展示当前状态和进度
4. 根据当前Phase，建议下一步操作
5. 按用户确认的方向推进

### 入口B：子skill回调

某个子skill（S1-S9）完成撰写后回调本skill。

**执行流程**：
1. 接收子skill传入的完成信息（section编号、完成状态、工作摘要）
2. 更新 `<output_path>/proposal-state.json` 中对应section的状态
3. 向 `<output_path>/proposal-log.md` 追加一条日志记录
4. 基于依赖关系，向用户给出下一步建议（继续迭代当前阶段 or 推进到下一个阶段）

## 输出路径与文件管理

### 初始化：确认输出路径

首次调用时（或 `proposal-state.json` 不存在时），必须向用户确认输出路径：

```
请确认申报书各部分的输出目录路径。
默认路径为当前工作目录下的 output/ 子目录。
```

确认后，在输出路径下创建以下结构：

```
<output_path>/
├── proposal-state.json    ← 状态管理文件
├── proposal-log.md        ← 撰写日志
├── 1-研究目的与意义.md
├── 2-国内外研究现状.md
├── 3-应用前景与学术价值.md
├── 4-参考文献.md
├── 5-研究基础与条件.md
├── 6-研究目标与内容.md
├── 7-研究方法.md
├── 8-可行性分析.md
└── 9-创新点与计划.md
```

### 子skill的输出规范

每个子skill完成撰写后，必须将内容写入 `<output_path>/<对应文件名>.md`。文件写入后才算该section完成。

## 状态管理

### 状态文件：proposal-state.json

```json
{
  "output_path": "用户确认的输出路径",
  "created_at": "创建时间 ISO格式",
  "last_updated": "最后更新时间 ISO格式",
  "project_info": {
    "topic": "研究主题",
    "field": "学科领域",
    "level": "申报级别",
    "duration": "项目执行期限"
  },
  "current_phase": 0,
  "sections": {
    "S1": {
      "status": "not_started",
      "file": "1-研究目的与意义.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S2": {
      "status": "not_started",
      "file": "2-国内外研究现状.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S3": {
      "status": "not_started",
      "file": "3-应用前景与学术价值.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S4": {
      "status": "not_started",
      "file": "4-参考文献.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S5": {
      "status": "not_started",
      "file": "5-研究基础与条件.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S6": {
      "status": "not_started",
      "file": "6-研究目标与内容.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S7": {
      "status": "not_started",
      "file": "7-研究方法.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S8": {
      "status": "not_started",
      "file": "8-可行性分析.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    },
    "S9": {
      "status": "not_started",
      "file": "9-创新点与计划.md",
      "iteration": 0,
      "last_updated": null,
      "notes": ""
    }
  },
  "ref_counter": 0
}
```

### 状态值说明

| 状态值 | 含义 |
|--------|------|
| `not_started` | 尚未开始撰写 |
| `in_progress` | 正在撰写中 |
| `draft_done` | 初稿完成，已写入输出文件 |
| `revised` | 经过修订，非首次撰写 |
| `finalized` | 已定稿，不再修改 |

### Phase 与 section 的对应

| Phase | 涉及section | Phase完成条件 |
|-------|-----------|-------------|
| 0 | S4 | S4至少 draft_done |
| 1 | S2, S1, S4 | S1和S2至少 draft_done |
| 2 | S3, S4 | S3至少 draft_done |
| 3 | S6 | S6至少 draft_done |
| 4 | S7, S4 | S7至少 draft_done |
| 5 | S9, S8, S5 | S5/S8/S9至少 draft_done |
| 6 | 全部 | 全部section finalized |

### 状态更新规则

子skill回调时，按以下规则更新状态：
1. 将对应section的 `status` 更新为传入的状态值
2. 将 `iteration` 加1
3. 将 `last_updated` 更新为当前时间
4. 根据Phase完成条件，判断是否推进 `current_phase`
5. 将整个文件的 `last_updated` 更新为当前时间

## 日志管理

### 日志文件：proposal-log.md

```markdown
# 申报书撰写日志

## [2026-04-11 14:30] Phase 0 - 文献调研
- **调用skill**: write-references (S4)
- **完成工作**: 收集了21篇参考文献，覆盖多模态安全评测、越狱攻击、安全对齐和后门防御四个方向
- **产出文件**: 4-参考文献.md
- **状态变更**: S4: not_started → draft_done

## [2026-04-11 15:00] Phase 1 - 研究现状
- **调用skill**: write-literature-review (S2)
- **完成工作**: 撰写了5条研究主线的文献综述，总结4点不足，预判4条发展趋势
- **产出文件**: 2-国内外研究现状.md
- **状态变更**: S2: not_started → draft_done

...
```

### 日志追加规则

每次子skill回调时，追加一条日志记录，包含：
- **时间戳**：当前日期时间
- **Phase和阶段描述**
- **调用的skill名称和编号**
- **工作摘要**：一句话概括做了什么（由子skill传入）
- **产出文件**：输出的文件名
- **状态变更**：section的状态变化

### 日志格式

```
## [YYYY-MM-DD HH:MM] Phase N - <阶段描述>
- **调用skill**: <skill-name> (SN)
- **完成工作**: <一句话摘要>
- **产出文件**: <filename>
- **状态变更**: SN: <旧状态> → <新状态>
```

## 执行前：信息收集

在首次启动（proposal-state.json 不存在）时，按以下顺序收集信息：

### 第一步：确认输出路径

向用户确认申报书输出目录。如用户未指定，建议默认路径。

### 第二步：收集项目信息

向用户收集以下基础信息，未提供的逐项询问：

**必需信息**：
1. **研究主题与方向**：项目研究什么？属于什么学科领域？
2. **核心研究问题**：要解决的核心问题是什么？
3. **申报类型与级别**：国自然青基/面上、省重点研发、研创项目等
4. **项目执行期限**：如"2027年1月-2029年12月"

**重要信息**（有则收集，没有后续补充）：
5. **已有材料**：开题报告、文献综述、相关论文、前期成果等文件路径
6. **研究方法初步思路**：打算用什么技术路线
7. **申请人/团队信息**：用于S5和S8
8. **指导教师信息**：姓名、职称、研究方向、代表性成果

### 第三步：创建状态文件和日志文件

将收集到的信息写入 `proposal-state.json`，创建空的 `proposal-log.md`。

## 依赖关系与执行顺序

### 依赖图

```
S4(文献调研) ────── 持续性任务，贯穿Phase 1-3
     │
     ▼
S2(研究现状) ◄──► S1(研究目的与意义)    ← Phase 1: 互为迭代
     │                   │
     └───────┬───────────┘
             ▼
        S3(应用前景与价值)              ← Phase 2
             │
             ▼
        S6(研究目标/内容/关键问题)       ← Phase 3: 核心枢纽
             │
             ▼
        S7(研究方法)                    ← Phase 4
        ┌────┴────┐
        ▼         ▼
  S9(创新+计划)  S8(可行性分析)          ← Phase 5: 可并行
                  │
                  ▼
              S5(研究基础/条件)           ← 随时可写
```

### 执行阶段

```
Phase 0: 前期准备
  └─ S4 启动文献调研

Phase 1: 立项依据核心（迭代，可循环2-3轮）
  ├─ S2 撰写国内外研究现状
  ├─ S1 撰写研究目的与意义（与S2迭代）
  └─ S4 补充文献

Phase 2: 价值论证
  ├─ S3 撰写应用前景和学术价值
  └─ S4 补充文献

Phase 3: 研究方案纲领
  ├─ S6 撰写研究目标、研究内容和拟解决的关键问题
  └─ Phase 1-2 完结后可对 S1/S2 做最终修订

Phase 4: 研究方法
  ├─ S7 撰写拟采取的研究方法
  └─ S4 最终确认文献引用

Phase 5: 收尾（以下可并行）
  ├─ S9 撰写特色创新点+计划+预期成果
  ├─ S8 撰写可行性分析
  └─ S5 撰写研究基础/条件/指导教师

Phase 6: 全文审查
  └─ 检查各section之间的逻辑一致性和衔接
```

## 每个Phase的执行规范

### Phase 0：前期准备

**目标**：建立文献基础，明确研究定位。

**执行步骤**：
1. 阅读用户提供的所有已有材料
2. 调用 `write-references` skill，启动文献调研
3. 配合 `academic-search` skill 进行文献检索
4. 整理初步文献列表，建立引用编号体系

**产出**：`4-参考文献.md`

### Phase 1：立项依据核心（迭代）

**目标**：完成"为什么要做这个研究"的论证。

**执行步骤**：
1. 调用 `write-literature-review` skill → `2-国内外研究现状.md`
2. 调用 `write-research-purpose` skill → `1-研究目的与意义.md`
3. **迭代检查**：S1与S2的逻辑一致性
4. 可能需要回到S4补充文献

### Phase 2：价值论证

**执行步骤**：
1. 调用 `write-application-prospects` skill → `3-应用前景与学术价值.md`
2. 检查S3与S1/S2的铺垫关系

### Phase 3：研究方案纲领

**执行步骤**：
1. 调用 `write-research-objectives` skill → `6-研究目标与内容.md`
2. 可配合 `write-key-problems` skill
3. **回溯修订**：对S1/S2/S3做最终修订

### Phase 4：研究方法

**执行步骤**：
1. 调用 `write-research-methods` skill → `7-研究方法.md`
2. 确认S4文献引用覆盖

### Phase 5：收尾

**执行步骤**（可并行）：
1. 调用 `write-innovation-plan` skill → `9-创新点与计划.md`
2. 调用 `write-feasibility` skill → `8-可行性分析.md`
3. 调用 `write-research-foundation` skill → `5-研究基础与条件.md`

### Phase 6：全文审查

**审查清单**：

| 检查项 | 涉及section |
|--------|-----------|
| S1核心问题 → S6目标 → S7方法 → S9创新点 | S1→S6→S7→S9 |
| S2不足 → S6内容 → S7方法 | S2→S6→S7 |
| S3价值 → S9成果 | S3→S9 |
| S4引用 ↔ 正文引用标记 | S4↔全文 |
| S5前期成果 → S6内容 | S5→S6 |
| S7条件需求 → S5/S8条件 | S7→S5/S8 |
| 全文术语一致性 | 全文 |
| 总字数 | 全文 |

## 下一步建议逻辑

子skill回调后，根据当前状态和依赖关系给出建议。建议规则：

### 建议优先级

1. **迭代当前阶段**：如果当前Phase的section刚完成初稿，建议先检查是否需要迭代（如Phase 1中S2完成后应检查S1是否需要调整）
2. **推进到下一阶段**：如果当前Phase所有section至少 draft_done，建议推进
3. **补充前置依赖**：如果某个section的前置依赖未完成，建议先完成依赖
4. **用户自由选择**：列出当前可执行的选项，让用户决定

### 建议输出格式

```
📋 当前进度：
- Phase N: <阶段名>
- S1: <状态> | S2: <状态> | ... | S9: <状态>

📌 下一步建议：
1. [推荐] <具体建议>
2. <备选建议>
3. <备选建议>

请选择下一步操作，或告诉我您想做什么。
```

## 迭代修订规范

### 何时触发迭代

1. **Phase内迭代**：S1↔S2之间的互相修订（Phase 1）
2. **跨Phase回溯**：后续Phase发现前置section需要调整
3. **用户反馈**：用户对某个section提出修改意见

### 迭代修订原则

- **向后传播优先**：修改了某个section后，检查后续依赖section是否需要调整
- **最小修改**：尽量只修改受影响的部分
- **状态更新**：每次修订后，将对应section状态更新为 `revised`，iteration加1

## 断点续写

如果会话中断，通过以下方式恢复：
1. 读取 `proposal-state.json`，确认当前Phase和各section状态
2. 读取 `proposal-log.md`，确认最后的工作内容
3. 读取已完成的section文件内容
4. 从中断处继续

## 注意事项

1. **不要跳过Phase 0**：文献调研是整个申报书的基础
2. **S1和S2互为迭代**：强行一次性写完任何一个都可能导致逻辑不一致
3. **S4是持续性任务**：每个Phase都可能需要补充新文献
4. **S5高度个性化**：必须基于申请人真实信息
5. **S6是核心枢纽**：承上启下，值得花最多时间打磨
6. **S7技术含量最高**：数学公式和算法设计直接影响评审判断
7. **全文审查不可省略**：各section分开撰写容易产生不一致
8. **尊重用户节奏**：每个Phase完成后与用户确认再继续
9. **每次状态变更都写日志**：保证断点续写的完整性
10. **子skill最后一步必须回调本skill**：确保状态和日志始终最新
