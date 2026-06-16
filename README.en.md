# Math Textbook to Skill

### A methodology for distilling mathematics textbooks into reusable AI skills — from definitions, theorems, proofs to exercises.

[![License: MIT](https://img.shields.io/badge/License-MIT-f5c542.svg)](./LICENSE)
[![Method: RIA-TV++ (Math)](https://img.shields.io/badge/Method-RIA--TV++%20Math-2ea44f.svg)](./SKILL.md)
[![Platform: Claude Code](https://img.shields.io/badge/Platform-Claude%20Code-f97316.svg)](https://code.claude.com/)

> This is the English version of `math-textbook-to-skill`, a fork of [cangjie-skill](https://github.com/kangarooking/cangjie-skill) adapted for mathematics textbooks.

## Why Mathematics Textbooks Need a Different Approach

Existing book-to-skill pipelines (like cangjie-skill) are designed for methodology books with loosely coupled principles and action steps. Math textbooks have a fundamentally different structure:

| Aspect | Methodology Books | Math Textbooks |
|--------|------------------|----------------|
| Knowledge units | Principles → Actions | Definitions → Theorems → Proofs → Examples |
| Relationships | Loosely coupled | Strict prerequisite dependencies |
| Core actions | Decision, choice, evaluation | Proof, calculation, counter-example construction |
| "Examples" | Author's life cases | Numbered mathematical examples |
| "Actions" | Step-by-step execution | Proof patterns & solution procedures |
| Boundaries | When not to apply | Counter-examples and edge cases |

## What It Does

The **RIA-TV++ (Math Edition)** pipeline converts a math textbook into a structured skill pack through 6+1 stages:

1. **Stage 0**: Full textbook understanding (enhanced with Top-10 Results + Prerequisites + Proof Path Map)
2. **Stage 1**: 7 parallel extractors (Theorem / Example / Counter-Example / Glossary / Proof Pattern / Dependency Graph / Exercise Classifier)
3. **Stage 1.5**: Triple verification adapted for math (pass rate 85-100%, value in dedup & dependency annotation)
4. **Stage 2**: Math RIA++ construction (Concept Quick Reference / Core Theorems / Typical Examples / Common Mistakes / Related Skills)
5. **Stage 3**: Zettelkasten linking with 4 relationship types (depends-on / uses / contrasts-with / generalizes)
6. **Stage 4**: Pressure testing with 4 prompt types (Direct / Boundary / Bait / Application)
7. **Bonus**: Exercise index

## Structure

```text
math-textbook-to-skill/
├── README.md
├── README.en.md             ← this file
├── LICENSE (MIT)
├── SKILL.md                 ← meta-skill definition
├── methodology/             ← stage-by-stage methodology docs
├── extractors/              ← 7 parallel extractor prompts
├── templates/               ← math-adapted templates
└── examples/                ← example: Linear Algebra Done Right
```

## Quick Start

```bash
git clone https://github.com/killerfirst/math-textbook-to-skill.git
cd math-textbook-to-skill

# Use as Claude Code skill
cp -r math-textbook-to-skill ~/.claude/skills/

# Or use as OpenClaw skill
cp -r math-textbook-to-skill ~/.openclaw/workspace/skills/
```

## Ecosystem

- [cangjie-skill](https://github.com/kangarooking/cangjie-skill) — General methodology distillation
- [darwin-skill](https://github.com/alchaincyf/darwin-skill) — Skill evolution framework
- **math-textbook-to-skill** (this repo) — Mathematics textbook distillation

## License

MIT. See [LICENSE](./LICENSE).
