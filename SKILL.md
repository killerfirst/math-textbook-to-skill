---
name: math-textbook-to-skill
description: Distill a mathematics textbook into a coherent set of executable AI skills. Use when the user asks to "拆数学书" / "把 XX 数学教材做成 skill" / "蒸馏数学教材" — i.e. wants a textbook's definitions, theorems, proofs, and exercises extracted into atomic, reusable skills. NOT for general methodology books, fiction, or simple textbook summaries.
---

# math-textbook-to-skill — 把数学教材蒸馏成一组可执行 skills 的元 skill

## 使命

把数学教材中沉淀的**定义 → 定理 → 证明 → 例题**体系，拆解成一组**原子化、可被 agent 在真实学习/解题/研究场景下调用**的 skills。

**边界**:
- ✅ 做: 数学教材（定义/定理/证明/例题/习题的蒸馏）
- ❌ 不做: 方法论/决策框架类书籍（请用 cangjie-skill）、简单书摘、非数学类教材

## 核心方法论: RIA-TV++（数学版）

与通用版 RIA-TV++ 同源，但每个阶段为数学教材特性做了重构。详见 `methodology/00-overview.md`。

```
阶段 0: Adler 整书理解（数学增强版）  → BOOK_OVERVIEW.md（含十大核心结果+证明路径图+预备知识清单）
阶段 1: 7 个 agent 并行提取           → 定理解/例题/反例/术语/证明模式/依赖图/习题分类
阶段 1.5: 三重验证（数学适配版）       → 去重 + 依赖重要性标注（通过率 85-100%）
阶段 2: 数学 RIA++ 构造               → 概念速查-核心定理-典型例题-常见错误-相关技能
阶段 3: Zettelkasten 链接（4 种关系） → INDEX.md（含 Mermaid 依赖图）
阶段 4: 压力测试（4 类 prompt）        → test-prompts.json + 本地跑 + 回炉
附加: 习题索引                        → 可选但强烈建议
```

## 何时调用此 skill

用户说类似:
- "帮我拆《线性代数应该这样学》"
- "把高等数学上册蒸馏成 skill"
- "distill this math textbook into skills: <path>"
- "我想把这本数学教材的方法论做成可用的 skill"

## 输入要求

在开始前**必须**从用户处确认:
1. **教材的文本来源**: PDF / EPUB / TXT / OCR 纯文本文件路径
2. **教材名 + 作者 + 版本/出版年**: 用于目录命名和审计
3. **目标读者水平**: 大一 / 考研 / 研究人员——影响技能粒度
4. **是否首次试点**: 如果用户是第一次用本 skill，建议先拆 1 章验证流程再批量

## 输出结构

```text
books/<book-slug>/
├── BOOK_OVERVIEW.md           # 阶段 0 产出：主旨/骨架/术语/十大结果/预备知识/证明路径/批判
├── INDEX.md                   # 阶段 3 产出：skill 总览 + Mermaid 依赖图 + 学习路径推荐
├── candidates/                # 阶段 1 产出：原始候选池（审计用）
│   ├── theorems.json
│   ├── examples.json
│   ├── counter-examples.json
│   ├── glossary.json
│   ├── proof-patterns.json
│   ├── dependency-graph.json
│   └── exercises.json
├── rejected/                  # 阶段 1.5 淘汰的单元 + 原因（审计用）
├── <skill-slug-1>/
│   ├── SKILL.md
│   └── test-prompts.json
├── <skill-slug-2>/
│   └── ...
```

## 执行流程（严格按顺序）

### 阶段 0 — 整书理解（数学增强版）

1. 读取用户提供的教材文本。大文件分块阅读。
2. 执行 `methodology/01-stage0-adler.md` 中的 Adler 四步 + 三项数学特有产出。
3. **数学特有产出**：
   - **十大核心结果**：全书最重要的 10 条定理（编号 + 一句话）
   - **预备知识清单**：读者需要先掌握什么
   - **证明路径图**：从哪些公理出发，经过哪些定理，最终到达哪些结论
4. 按 `templates/BOOK_OVERVIEW.md.template` 填充，写入 `books/<slug>/BOOK_OVERVIEW.md`。
5. 把产出展示给用户确认："骨架我理解对了吗？依赖关系图对吗？" 得到确认再进入阶段 1。

### 阶段 1 — 7 个 sub-agent 并行提取

并行 spawn 7 个 Task sub-agents：

| sub-agent | 读取的 prompt | 产出 |
|-----------|--------------|------|
| 定理提取器 | `extractors/theorem-extractor.md` | 定义、定理、引理、推论 |
| 例题提取器 | `extractors/example-extractor.md` | numbered examples + 解题步骤 |
| 反例提取器 | `extractors/counter-example-extractor.md` | 反例 + 边界情形 + 常见误解 |
| 术语提取器 | `extractors/glossary-extractor.md` | 概念名 + 精确定义 + 直观解释 |
| 证明模式提取器 | `extractors/proof-pattern-extractor.md` | 反复出现的证明结构 |
| 依赖图提取器 | `extractors/dependency-graph-extractor.md` | 定理间的使用和被使用关系 |
| 习题分类器 | `extractors/exercise-classifier.md` | 按类型和难度分类的习题索引 |

每个 sub-agent 独立读书、独立提取、独立输出。

### 阶段 1.5 — 三重验证（数学适配版）

读取 `methodology/03-stage1.5-triple-verify.md`，对每个候选执行：

- **V1 跨域**：定理是否至少被另一章的定理引用过？例题体现的方法是否有 ≥2 个变体？
- **V2 预测力**：该定理/方法能否解决同类型的变体题目（而非仅原文那一道）？
- **V3 独特性**：低年级教材中不是高中已知内容；高年级教材中不是本领域基础公理

通过的进入阶段 2。不通过的写入 `rejected/` 并附原因。

### 阶段 2 — 数学 RIA++ 构造 skill

对每个通过的语义单元，按 `templates/SKILL.md.template` 填充：

| 段 | 内容 | 用途 |
|---|------|------|
| 概念速查 | 表格形式的速查卡片（定义 + 性质 + 关键点） | 快速定位概念 |
| 核心定理 | 编号 + 定理名 + 要点（一行概括），表格形式 | 扫描定位、快速引用 |
| 典型例题 | 题目 + 解题步骤 (1-2-3) + 教学意图 | 理解定理该如何用 |
| 常见错误 | ❌ 错误 + 为什么错 + 正确做法 | 典型踩坑提醒 |
| 相关技能 | Wiki-link 到其他技能模块，含关系类型 | 构建知识网络 |

细则见 `methodology/04-stage2-math-ria.md`。

### 阶段 3 — Zettelkasten 链接（4 种关系）

按 `methodology/05-stage3-zettelkasten.md`：
1. 找出 skill 之间的**4 种关系**：依赖 / 使用 / 对比 / 推广
2. 在每个 SKILL.md 末尾补"相关技能"段
3. 按 `templates/INDEX.md.template` 生成 INDEX.md（含 Mermaid 依赖图）

### 阶段 4 — 压力测试

对每个 skill 按 `methodology/06-stage4-pressure-test.md`：
1. 设计 10–12 条测试 prompt，按 `templates/test-prompts.json.template` 写入
2. 必须包含 4 类：**直接提问** (50%) / **边界模糊** (30%) / **诱饵** (10%) / **应用场景** (10%)
3. 特别关注 F=R 和 F=C 的域区分测试
4. 本地跑一遍，未过的回炉重做阶段 2
5. 全部通过后通知用户

## 质量红线（违反则阻止输出）

1. 每个 skill 必须有完整的概念速查 / 核心定理 / 典型例题 / 常见错误 四段
2. 每个 skill 必须标注前置依赖 skill
3. 每个 skill 必须通过全部三重验证
4. 每个 skill 必须有 test-prompts.json，包含诱饵测试
5. 未经验证的依赖图不得发布
6. F=R 和 F=C 混用的定理必须显式标注

## 与 cangjie-skill 的生态定位

- **cangjie-skill**：通用方法论蒸馏（原则/框架/决策类书籍）
- **math-textbook-to-skill**（本 skill）：数学教材蒸馏（定义/定理/证明/例题类教材）
- **darwin-skill**：进化任意 skill

三者咬合：cangjie 负责通用方法论，math-textbook 负责数学体系，darwin 让所有 skill 持续进化。

## 调用惯例

- **永远先试点 1 章** — 除非用户明确说"批量"
- **阶段之间主动汇报进度** — 不要静默跑完再 dump 结果
- **不凭记忆拆书** — 没文本就停下来问
- **保留审计轨迹** — 每个候选的依赖关系必须标注
