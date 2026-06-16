# 示例：《线性代数应该这样学》— 适应验证

本文档是 `math-textbook-to-skill` 流水线的示例验证。
原始适配基于 Sheldon Axler 的《Linear Algebra Done Right》（第四版）。

## 为什么选这本书作为示例

1. **结构清晰**：定义→定理→证明→例题的四步结构极为完整
2. **证明路径独特**：Axler 以"不含行列式"的路径闻名，证明路径图特别适合展示
3. **依赖关系明确**：各章之间有严格的依赖链

## 示例产出

（待创建——可基于本流水线运行后生成）

- `linear-algebra-done-right/B00K_OVERVIEW.md`
- `linear-algebra-done-right/INDEX.md`
- `linear-algebra-done-right/01-vector-spaces/SKILL.md`
- `linear-algebra-done-right/02-linear-maps/SKILL.md`
- ...

## 预期 Skill 划分

| 技能 | 章节 | 核心内容 |
|------|------|---------|
| 向量空间与子空间 | Ch1 | 向量空间定义、子空间判定、和与交 |
| 基与维数 | Ch2 | 线性无关/相关、基、维数 |
| 线性映射 | Ch3A-3C | 线性映射定义、零空间与像空间、维数公式 |
| ... | ... | ... |

## 从示例到通用

此示例展示了如何将本流水线应用于任何数学教材。核心三问：

1. **依赖图画出来了吗？**——没有依赖图的数学 skill 是不完整的
2. **F=R 和 F=C 分开了吗？**——不分开的数学 skill 会给出错误答案
3. **定理陈述精确吗？**——漏了"有限维"限定词等于漏了半个定理
