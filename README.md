# AI 驱动的科研申报书撰写 Skills

> 一套基于 Claude Code 的科研申报书全流程撰写工具链。从文献调研到成稿，覆盖申报书 9 大板块，支持人工监督和全自动两种模式。

## 它能做什么

想象这个场景：你有一个研究想法，但面对一份结构复杂的申报书无从下手——文献综述怎么组织、研究目的的论证链怎么搭、关键问题怎么凝练、研究方法怎么写才不像"水"……

这套 skills 做的就是这件事：**把你的研究想法，变成一份逻辑严密、结构完整的申报书**。

它不是一个简单的模板填充器。每个 skill 内嵌了学术写作的方法论——

- **write-research-purpose**：教你用"宏观趋势→特殊性论证→问题凝练"的三层漏斗来写研究目的，而不是泛泛地说"这个问题很重要"
- **write-key-problems**：用"现实困境→特殊性→已有证据→问题凝练"五层递进结构，让评审读完每个关键问题后同时理解"为什么重要、为什么难、为什么可行、具体做什么"
- **write-literature-review**：按研究主线组织文献脉络，自动引出不足，自然衔接你的研究切入点
- **write-research-methods**：从问题建模到算法设计，包含公式撰写规范，展现技术深度
- ……每个 section 都有对应的写作方法论

**覆盖的 9 大板块（按申报书结构排列）**：

| 板块 | Skill | 一句话说明 |
|------|-------|-----------|
| 研究目的与意义 | `write-research-purpose` | 三层论证链：为什么做、有什么用 |
| 国内外研究现状 | `write-literature-review` | 按主线梳理文献，引出研究不足 |
| 应用前景与学术价值 | `write-application-prospects` | 从学术、工程、行业三个层面论证价值 |
| 参考文献 | `write-references` | 系统性文献调研与格式编排 |
| 研究基础与条件 | `write-research-foundation` | 展示团队能力和实验条件 |
| 研究目标与内容 | `write-research-objectives` | 目标→内容→关键问题的递进框架 |
| 研究方法 | `write-research-methods` | 问题建模、算法设计、公式推导 |
| 可行性分析 | `write-feasibility` | 技术储备+研究条件+团队支撑三维论证 |
| 创新点与计划 | `write-innovation-plan` | 创新凝练+年度计划+预期成果 |

另有 **`write-proposal`** 作为编排总指挥，管理 9 个 skill 的执行顺序、依赖关系和迭代修订；**`academic-search`** 提供学术论文搜索能力（arXiv、Semantic Scholar、Google Scholar、知网等）。

## 安装

### 前置条件

- 安装 [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)（或使用 VS Code / JetBrains 插件）
- 拥有 Anthropic API Key 或 Claude Pro/Max 订阅

### 安装步骤

1. **克隆仓库到项目目录**

   在你的项目根目录下执行：

   ```bash
   # 将仓库克隆为 .claude 目录（注意目标路径）
   git clone git@github.com:Maxine-1520/OpenAIR_proposal.git .claude
   # 或者
   git clone git@code.gitlink.org.cn:Kexing/OpenAIR_proposal.git .claude
   ```

   克隆完成后，项目结构如下：

   ```
   your-project/
   └── .claude/
       └── skills/
           ├── write-proposal/
           │   └── SKILL.md
           ├── write-research-purpose/
           │   └── SKILL.md
           ├── write-literature-review/
           │   └── SKILL.md
           ├── ...（其余 10 个 skill）
           └── academic-search/
               ├── SKILL.md
               └── references/...
   ```

2. **确认 Claude Code 能识别**

   在项目目录下启动 Claude Code，skills 会被自动加载。你可以通过对话中的关键词触发，也可以直接提到 skill 名称。

3. **（可选）配置 academic-search**

   如果需要使用学术搜索功能（Google Scholar、知网等），需要额外配置：
   - 安装 Node.js 22+
   - 运行 `bash ~/.claude/skills/academic-search/scripts/check-deps.sh` 检查依赖
   - Google Scholar 需要 Chrome 远程调试模式（详见 `academic-search/SKILL.md`）

## 使用说明

### 模式一：人工监督模式（推荐新手）

你全程掌控，一步步推进。每个 section 写完后你审查、提出修改意见，满意了再继续下一个。

**启动**：

```
帮我写一份关于"多模态大模型安全性"的申报书，级别是省级研创项目，
执行期限 2027年1月至2029年12月。
```

Claude 会启动 `write-proposal` skill，收集项目信息，确认输出路径，然后按 Phase 推进：

```
📋 当前进度：
- Phase 0: 前期准备
- S1: not_started | S2: not_started | ... | S9: not_started

📌 下一步建议：
1. [推荐] 开始文献调研（S4），建立文献基础
2. 先提供更多背景材料再开始
3. 跳到某个具体 section

请选择下一步操作，或告诉我您想做什么。
```

**典型对话流**：

```
你：先帮我收集一些关于多模态安全领域的核心论文
    → 触发 academic-search + write-references

你：开始写研究目的与意义
    → 触发 write-research-purpose，产出 1-研究目的与意义.md

你：第二部分写得太学术了，能不能更突出实际应用价值
    → 触发修订，迭代优化

你：继续推进下一个 section
    → 按依赖关系建议下一步

...直到全部 9 个 section 完成
```

**单 section 直接调用**：

如果你只需要写某一个部分：

```
帮我写"拟解决的关键问题"部分，研究方向是多模态后门攻击的检测与净化，
研究内容包括：训练数据净化、推理时防御、测试时检测
```

```
帮我写文献综述，主题是大模型安全对齐，我已经有了以下论文列表：...
```

### 模式二：全自动模式（适合有经验的用户）

你给一个目标，Claude 自主编排所有 skill，一路推进直到申报书完成。中间可以做自我评审，每次产出后自动 git 提交。

**全自动 prompt 示例**：

```
接下来进入全自动环节，我要去睡觉休息了，明天早上验收你们的申报书产出。
全自动环节里，在output_v2/路径下创建本地仓库，每次写日志后都做git提交；
仅允许所有在output_v2/路径下的各种操作；
所有下一步操作都按你推荐的来；
每次产出完，需要评审（自我反思 或者 新开一个sub-agent评审）。
确认理解，确认可行，向我报告你的理解和可行性分析，待我确认后开始全自动行动，
直到申报书各部分产出完。
```

**更简洁的全自动指令**：

```
全自动模式：从文献调研开始，按推荐顺序完成申报书所有 section。
每个 section 完成后自审一轮再继续。输出到 output/ 目录。
研究主题：X，申报级别：Y。
```

**全自动 + 现有材料**：

```
全自动写申报书，我已准备好以下材料：
- 开题报告：docs/开题报告.pdf
- 文献笔记：docs/literature-notes.md
- 前期实验结果：docs/preliminary-results.md
研究主题是多模态安全评测，级别是国自然青基。
按你推荐的顺序自动推进，所有产出写入 output/ 目录。
```

### 执行流程（发生了什么）

无论哪种模式，底层都遵循这个 Phase 推进逻辑：

```
Phase 0: 文献调研 ──────── 建立文献基础
Phase 1: 立项依据核心 ──── 研究现状 ↔ 研究目的（互为迭代）
Phase 2: 价值论证 ──────── 应用前景与学术价值
Phase 3: 研究方案纲领 ──── 研究目标、内容、关键问题
Phase 4: 研究方法 ──────── 技术路线、算法设计
Phase 5: 收尾 ──────────── 创新点、可行性、研究基础（可并行）
Phase 6: 全文审查 ──────── 检查逻辑一致性和术语统一
```

每个 Phase 完成后，状态和日志会自动更新到 `proposal-state.json` 和 `proposal-log.md`，支持断点续写——即使会话中断，下次启动时自动恢复进度。

### 输出结构

完成后你会得到：

```
output/
├── proposal-state.json          ← 状态追踪（可查看进度）
├── proposal-log.md              ← 撰写日志（每步操作记录）
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

## 常见问题

**Q：需要提供多少材料才能开始？**

最少只需要一个研究方向。Claude 会配合 `academic-search` skill 自动检索文献。但如果你有开题报告、文献笔记等现有材料，产出质量会更高。

**Q：能写什么级别的申报书？**

国自然（青基/面上/重点）、省基金、研创项目、开题报告等均可。不同级别的论述深度和侧重点不同，启动时告知级别即可。

**Q：全自动模式的产出质量可靠吗？**

全自动模式适合产出初稿。建议全自动产出后，用人工监督模式逐 section 审查修订。每个 skill 内嵌了质量自检逻辑，全自动模式下也会自动触发。

**Q：支持哪些学科的申报书？**

这套 skills 的写作方法论是学科无关的（论证链构建、五层递进结构、先立后破等）。已有实例集中在 AI/计算机科学方向，但框架适用于任何理工科申报书。
