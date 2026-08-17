# LaTeX 插图、表格、编译与版面工作流

本文件不讲 Python 或 PGFPlots 绘图代码。图的通用逻辑读取 `figure-guidance.md`；绘图实现读取对应技术指南。本文件只负责图表进入论文后的环境、浮动、编译和页面质量。

## 1. 编辑原则

项目使用 SeuThesiY。保持主文件、章节输入、标签、引用键、图表路径和已有宏包稳定。非必要不在 `seuthesiY.tex` 新增宏包，不通过全局设置解决单个局部问题。任何排版修改都遵循：修改—编译—读日志—渲染页面—目视检查—迭代。

## 2. 插图环境

沿用项目既有 `figure`、`sidewaysfigure` 和 `subfloat` 结构。修改图形文件或拆分子图时，同步更新 `\caption`、`\label`、正文 `\ref` 和分析顺序。外部 PDF/SVG/PNG 的路径应位于 `figures/`；原生 PGFPlots 则保留源码和 `data/` 路径。

不要非等比拉伸图。宽而矮的图即使占满 `\textwidth` 仍会留下空白，应回到绘图源调整纵横比。最终判断以论文页面上的实际尺寸为准。

## 3. 编译层级

快速语法和局部布局检查使用 XeLaTeX，并优先输出到临时目录。涉及目录、引用、参考文献或索引的完整编译使用：

`XELATEX → MAKEINDEX → BIBER → XELATEX → XELATEX`

环境具备时可使用 `latexmk` 驱动 XeLaTeX。项目含参考文献、索引和复杂模板，不优先采用简单 Tectonic 路线。确认从主文件和正确工作目录构建。

## 4. 日志诊断

1. 先检查致命错误与停止位置。
2. 检查 undefined reference/citation，区分需要重编译和键不存在。
3. 检查 `Float too large for page`、`Overfull \vbox` 和 `Overfull \hbox`。
4. 区分本次新增错误和项目既有警告。
5. 编译成功后仍要检查 PDF 目标页面和目录。

常见引用错误包括中文标点或自然语言进入 `\cite{}`。修复源头，不以删除引用或跳过编译掩盖问题。

## 5. 页面诊断

将目标页或全篇 PDF 渲染为 PNG，必要时制作缩略图总览，检查大面积空白、孤立标题、图文距离过远、浮动体延迟、横向图裁切、表格跨页和字体不一致。问题页再以较高分辨率查看。

## 6. 浮动体

一般沿用 `[!htbp]`、`[!htb]` 或已有专用位置参数。不要无差别使用 `[H]`、`\clearpage` 或 `\FloatBarrier`。

浮动页普遍过于松散时，可在确认全局影响后将浮动体靠上：

```tex
\makeatletter
\setlength{\@fptop}{0pt}
\setlength{\@fpsep}{14pt}
\setlength{\@fpbot}{0pt plus 1fil}
\makeatother
\renewcommand{\topfraction}{0.95}
\renewcommand{\bottomfraction}{0.95}
\renewcommand{\textfraction}{0.06}
\renewcommand{\floatpagefraction}{0.80}
```

标题与图分离通常源于大图积压。先调整源图高度，使“标题＋引导段＋图＋图题”能够同页；仍需固定时，才在确认高度足够后使用 `\clearpage` 与 `[H]`。`\FloatBarrier` 可阻止跨节浮动，但也可能制造孤立标题，不能单独作为万能修复。

## 7. 多面板与大图

宽面板不宜硬塞入 2×2 网格。内容密集时采用纵向堆叠、独立图页或拆图。轻微 `Float too large` 时先减少面板间距、图题间距或整体宽度 1%—2%，不要先缩小字体。横置图仅用于无法竖版表达的宽表或复杂图。

## 8. 表格

- 单位、有效数字和小数位统一。
- 优先使用 `booktabs`，避免密集竖线。
- 表头过长时调整列宽、换行或措辞，不先使用极小字体。
- 超宽表先精简列、调整列间距和列类型；`\resizebox` 仅作最后手段。
- 跨页长表使用模板已有长表环境；旋转表格检查阅读方向、页眉和页脚。

## 9. 最终验收

- 主文件按适当流程编译成功。
- 无本次引入的 undefined reference/citation、浮动体过大或溢出。
- 图、表、公式、目录和交叉引用正确。
- 目标页已渲染并目视检查，图文距离和页面密度合理。
- 编译产物没有覆盖用户需要保留的版本；临时文件位于安全目录。
- 完整编译受限时，明确已完成的静态检查和仍未验证的部分。
