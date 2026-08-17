# Thesis Research Workflow

[中文说明](#中文说明) · [English](#english)

## 中文说明

这是一个从中国工科博士论文实践中蒸馏而来的、自包含的 Codex Skill。它将研究者在长期论文工作中形成的思考方式、行文习惯与质量控制流程整理为可复用指南，覆盖证据边界内的科研推理、中文学术写作、文献与引用管理、科研图表设计、Python/Matplotlib 绘图、TikZ/PGFPlots 绘图，以及 LaTeX 编译与版面检查。

### 内容结构

- `SKILL.md`：Skill 的入口、任务路由与核心工作流
- `agents/openai.yaml`：Codex 界面元数据
- `references/`：按任务类型拆分、按需读取的专题指南

其中包括：

- 研究问题拆解与证据约束
- 博士论文的论证组织和中文行文风格
- 文献、引用与可核查性要求
- 通用科研图片设计原则
- Python/Matplotlib 科研绘图技术指南
- TikZ/PGFPlots 科研绘图技术指南
- LaTeX 排版、编译与版面质量控制
- 交付前质量门与检查清单

### 安装与使用

将本仓库复制或克隆到 Codex 的 skills 目录，并保持 `SKILL.md`、`agents/` 与 `references/` 的相对目录结构不变。安装后，可通过 `$thesis-research-workflow` 调用。

### 适用范围

本 Skill 源于工程学博士论文工作流，但其核心方法——证据优先、结论受证据约束、章节服务于论证、图表服务于结论、交付前逐项核验——也可以迁移到其他研究领域。具体术语、图表规范和 LaTeX 约定可根据学科与院校要求调整。

## English

A self-contained Codex skill distilled from a Chinese engineering doctoral-thesis workflow. It provides reusable guidance for evidence-bounded research reasoning, Chinese scientific writing, literature and citation hygiene, figure design, Python/Matplotlib plotting, TikZ/PGFPlots plotting, and LaTeX compilation and layout review.

### Contents

- `SKILL.md` — routing and core workflow
- `agents/openai.yaml` — Codex interface metadata
- `references/` — focused guidance loaded by task type

### Installation

Copy or clone this repository into your Codex skills directory, keeping `SKILL.md`, `agents/`, and `references/` together. Then invoke it as `$thesis-research-workflow`.

### Scope

The skill reflects an evidence-first engineering-research workflow. Project-specific terminology and LaTeX conventions can be adapted for other disciplines while preserving the general reasoning and quality-control structure.
