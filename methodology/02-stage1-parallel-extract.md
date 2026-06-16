# 阶段 1 — 7 个 sub-agent 并行提取

## 目标

不是用单一视角读一遍，而是**同时从 7 个不同角度扫描全书**，最大化候选单元覆盖率。

## 与通用版的差异

通用版 cangjie-skill 使用 5 个提取器（框架/原则/案例/反例/术语）。数学版：

- **保留 2 个**：反例提取器、术语提取器
- **改编 2 个**：框架→证明模式、原则→定理、案例→例题
- **新增 3 个**：依赖图提取器、习题分类器

| 通用版 | 数学版 | 改动 |
|--------|--------|------|
| 框架提取器 | 证明模式提取器 | 思维模型 → 证明结构 |
| 原则提取器 | 定理提取器 | 原则/规则 → 定理/定义 |
| 案例提取器 | 例题提取器 | 作者实例 → 编号例题 |
| 反例提取器 | 反例提取器 | 保留（核心） |
| 术语提取器 | 术语提取器 | 保留（增强） |
| — | 依赖图提取器 | 新增：定理间的使用关系 |
| — | 习题分类器 | 新增：按类型/难度分类 |

## 7 个 sub-agent

每个 sub-agent 接收：
- `BOOK_OVERVIEW.md`（阶段 0 产出）
- 教材文本
- 对应的 extractor prompt

| # | extractor | 查找对象 | 产出文件 |
|---|-----------|---------|---------|
| 1 | theorem-extractor | 定义、定理、引理、推论 | `candidates/theorems.json` |
| 2 | example-extractor | numbered examples + 解题步骤 | `candidates/examples.json` |
| 3 | counter-example-extractor | 反例 + 边界情形 + 常见误解 | `candidates/counter-examples.json` |
| 4 | glossary-extractor | 概念名 + 精确定义 + 直观解释 | `candidates/glossary.json` |
| 5 | proof-pattern-extractor | 反复出现的证明结构 | `candidates/proof-patterns.json` |
| 6 | dependency-graph-extractor | 定理间的使用和被使用关系 | `candidates/dependency-graph.json` |
| 7 | exercise-classifier | 习题按类型/难度分类 | `candidates/exercises.json` |

## 并行执行

在一次调用中通过 Agent 工具**同时 spawn 7 个**，不是串行。

## 每个候选单元的最小字段

```yaml
id: t01                           # 类型缩写 + 序号
title: 维数公式                    # 定理/概念名
type: theorem                     # theorem / example / counter-example / term / proof-pattern
source_chapter: 第 3 章            # 书中位置
source_quote: |                   # 原文引用（定理陈述必须精确）
  "设 V 是有限维向量空间，T∈L(V,W)，则 dimV = dim null T + dim range T"
summary: |                        # 用自己的话，5-10 行
  ...
dependencies: [t00]               # 依赖哪些前置定理
used_by: [t02, t03]              # 被哪些后续定理使用
tags: [linear-map, dimension]
```

## 输出前的自检

1. 这个单元在书中有明确根据吗？（不是我脑补）
2. 它属于我这个 extractor 的职责范围吗？
3. 是不是已经在别处被别的 extractor 提取过了？（重复不是问题，阶段 1.5 会合并）

## 不在本阶段做的事

- **不做筛选** — 宁错杀，留给阶段 1.5
- **不写 skill** — 只出候选
- **不解决依赖** — 只标注依赖
