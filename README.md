<div align="center">

# Math Textbook to Skill

### 把数学教材的「定义→定理→证明→例题」，蒸馏成可调用的 AI Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-f5c542.svg)](./LICENSE)
[![Method: RIA-TV++ (Math)](https://img.shields.io/badge/Method-RIA--TV++%20Math-2ea44f.svg)](./SKILL.md)
[![Platform: OpenClaw](https://img.shields.io/badge/Platform-OpenClaw-1677ff.svg)](https://github.com/openclaw/openclaw)
[![Platform: Claude Code](https://img.shields.io/badge/Platform-Claude%20Code-f97316.svg)](https://code.claude.com/)

**学完一章数学，带走一套能调用的推理工具。**

</div>

## 为什么数学教材需要单独的技能蒸馏方法

现有的 [cangjie-skill](https://github.com/kangarooking/cangjie-skill)（即 book to skill）是一个优秀的通用方法论蒸馏流水线，但它为「决策框架/原则/清单」类书籍设计。数学教材的知识结构完全不同：

| 维度 | 方法论书（《影响力》） | 数学教材（《线性代数应该这样学》） |
|------|----------------------|----------------------------------|
| 知识单元 | 原则 → 行动步骤 | 定义 → 定理 → 证明 → 例题 |
| 单元关系 | 松散耦合 | 严格前置依赖 |
| 核心动作 | 决策、选择、评估 | 证明、计算、构造反例 |
| "案例"是什么 | 作者用过的实例 | 典型例题（numbered examples） |
| "行动"是什么 | 1-2-3 可执行步骤 | 证明模式 + 解题步骤 |
| 边界条件 | 不适用场景 | 定理的假设条件反例 |

**所以 math-textbook-to-skill 不是简单改 prompt，而是整套方法论的重构** —— 从提取器类型、验证标准、RIA++ 结构到测试用例，全部针对数学教材重新设计。

适配细节详见 [你的适配笔记](./ADAPTATION_NOTES.md)。

## 它解决了什么问题

- 学完数学定理不知道什么时候用 → 每个 skill 有明确的触发关键词 + 适用条件
- 定理之间依赖关系不清晰 → 依赖图提取器 + INDEX.md 可视化
- 看懂了证明但自己不会做 → 证明模式提取器提炼反复出现的套路
- 总在同一个反例上翻车 → 常见错误段 + 边界条件反例

## 它是怎么工作的

使用 **RIA-TV++ (数学版)** 流水线，共 6+1 个阶段：

```
阶段 0: 整书理解       → 额外产出[十大核心结果 + 预备知识清单 + 证明路径图]
阶段 1: 7 个提取器并行   → 定理/例题/反例/术语/证明模式/依赖图/习题
阶段 1.5: 三重验证适配   → 85-100% 通过率（价值在去重而非淘汰）
阶段 2: 数学 RIA++ 构造  → 概念速查 / 核心定理 / 典型例题 / 常见错误 / 相关技能
阶段 3: Zettelkasten 链接 → 4 种关系：依赖 / 使用 / 对比 / 推广
阶段 4: 压力测试         → 直接提问 / 边界模糊 / 诱饵 / 应用场景 四类
附加: 习题索引           → 可选，强烈建议
```

## 与 cangjie-skill 的差异

| 模块 | cangjie-skill（通用版） | math-textbook-to-skill（数学版） |
|------|----------------------|--------------------------------|
| 提取器 | 5 个：框架/原则/案例/反例/术语 | 7 个：**定理/例题**/反例/术语/**证明模式/依赖图/习题分类** |
| RIA++ 六段 | R-I-A1-A2-E-B | 概念速查-核心定理-典型例题-常见错误-相关技能 |
| 关键新增 | — | 十大核心结果、证明路径图、习题索引 |
| 验证通过率 | 25-50% | 85-100%（去重为主） |
| 测试分类 | 应调用/不应调用/边界模糊 | + **应用场景**、区分域 F=R 和 F=C |
| 前置依赖 | 无 | 预备知识清单必须在阶段 0 明确 |

## 快速开始

```bash
# Clone 本仓库
git clone https://github.com/weijinfeng177/math-textbook-to-skill.git
cd math-textbook-to-skill

# 在你自己的 workspace 中调用 SKILL.md
# 方法 A：作为 Claude Code Skill 使用
cp -r math-textbook-to-skill ~/.claude/skills/

# 方法 B：作为 OpenClaw Skill 使用
cp -r math-textbook-to-skill ~/.openclaw/workspace/skills/
```

## 使用方法

1. **准备数学教材的文本**（PDF/EPUB/TXT/OCR 后的纯文本）
2. **运行阶段 0**：读完整本书，产出 BOOK_OVERVIEW.md
3. **并行阶段 1**：启动 7 个 sub-agent 提取器
4. **阶段 1.5 验证**：去重、标注依赖重要性
5. **阶段 2 构造**：按数学版模板生成 SKILL.md
6. **阶段 3-4**：链接 + 压力测试
7. （可选）**习题索引**附录

详细流程见 `methodology/` 目录和 `SKILL.md`。

## 已生成的 Skill Packs（示例）

| 来源教材 | Skills 数 | 仓库 |
|---------|-----------|------|
| 线性代数应该这样学（Axler） | 10 | （待创建） |

## 仓库结构

```text
math-textbook-to-skill/
├── README.md                  ← 中文说明（你正在看）
├── README.en.md               ← English version
├── LICENSE                    ← MIT
├── SKILL.md                   ← 元 skill 定义（完整执行规范）
├── GITHUB_REPO.md             ← GitHub 仓库元数据
├── ADAPTATION_NOTES.md        ← 你的原始适配笔记
├── methodology/               ← RIA-TV++（数学版）各阶段方法论文档
├── extractors/                ← 7 个并行提取器的 prompt 定义
├── templates/                 ← 数学版的 SKILL.md / INDEX.md / BOOK_OVERVIEW.md 模板
└── examples/                  ← 示例：以《线性代数应该这样学》为例
```

## 生态位置

```
cangjie-skill（通用方法论蒸馏）
      ↕
math-textbook-to-skill（数学教材蒸馏） ← 你在这里
      ↕
📐 math-skill-packs（实际产出的技能包仓库）
```

- [cangjie-skill](https://github.com/kangarooking/cangjie-skill) — 通用方法论蒸馏
- [darwin-skill](https://github.com/alchaincyf/darwin-skill) — 进化任意 skill
- **math-textbook-to-skill**（本仓库）— 数学教材蒸馏

## 许可证

MIT。见 [LICENSE](./LICENSE)。

## 贡献

欢迎 PR！如果你用这个流水线蒸馏了某本数学教材，欢迎提交 example 或创建你自己的 skill pack 仓库。
