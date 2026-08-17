# TikZ/PGFPlots 科研绘图技术指南

使用本文件前先读取 `figure-guidance.md`。本文件只说明 PGFPlots 实现；插图浮动、表格和整篇编译由 `latex-workflow.md` 负责。

## 1. 项目色卡

以下代码来自 `seuthesiY.tex`。颜色名称和 RGB/HTML 值是项目内权威定义：

```tex
\definecolor{darkgray}{RGB}{51,51,51}      % #333333 深灰
\definecolor{bluecolor}{RGB}{90,174,243}   % #5AAEF3 蓝色
\definecolor{redcolor}{RGB}{230,90,86}     % #E65A56 红色
\definecolor{greencolor}{RGB}{27,158,119}  % #1B9E77 绿色
\definecolor{purplecolor}{RGB}{109,97,228} % #6D61E4 紫色
\definecolor{orangecolor}{RGB}{255,151,76} % #FF974C 橙色
\definecolor{pinkpurple}{RGB}{204,69,244}  % #CC45F4 粉紫

\definecolor{deepblue}{HTML}{3498DB}       % 边框深蓝
\definecolor{lightblue}{HTML}{AED6F1}      % 填充浅蓝
\definecolor{vaporred}{HTML}{E74C3C}       % 气相红
\definecolor{mixedgreen}{HTML}{27AE60}     % 同化绿
```

`greencolor` 的 RGB 定义对应 `#1B9E77`；以实际 RGB 为准。章节绘图优先使用这些颜色，不另建含义重复的局部色卡。连续色图确需新增颜色时，应避免与主要工况曲线混淆。

## 2. 项目轴与序列样式

项目主文件已定义以下样式，章节中直接调用，不重复粘贴定义：

```tex
\pgfplotsset{compat=1.18}
\pgfplotsset{
  thesispubaxis/.style={
    width=14cm,
    height=8cm,
    axis lines*=box,
    clip mode=individual,
    tick align=inside,
    tick style={black},
    tick label style={font=\small},
    legend style={
      at={(0.05,0.95)},
      anchor=north west,
      fill=white,
      fill opacity=0.92,
      text opacity=1,
      draw=gray!35,
      rounded corners=1.5pt,
      font=\small
    },
    legend cell align=left,
    major grid style={line width=0.4pt, draw=gray!32},
    minor grid style={line width=0.2pt, draw=gray!14},
    grid style={line width=0.3pt, draw=gray!20},
    xmajorgrids=true,
    ymajorgrids=true,
    xminorgrids=true,
    yminorgrids=true,
    minor tick num=1,
    enlarge x limits=0.05,
    enlarge y limits=0.05,
    every axis plot/.append style={line cap=round, line join=round},
  },
  thesisseries/.style={
    line width=1.8pt,
    mark size=3.2pt,
    mark options={line width=0.7pt, solid}
  },
  thesisgrayseries/.style={
    thesisseries, color=darkgray, mark=*,
    mark options={draw=darkgray!90!black, fill=darkgray!22, line width=0.7pt}
  },
  thesisredseries/.style={
    thesisseries, color=redcolor, mark=triangle*, mark size=3.9pt,
    mark options={draw=redcolor!90!black, fill=redcolor!22, line width=0.7pt}
  },
  thesisblueseries/.style={
    thesisseries, color=bluecolor, mark=square*,
    mark options={draw=bluecolor!90!black, fill=bluecolor!22, line width=0.7pt}
  },
  thesisgreenseries/.style={
    thesisseries, color=greencolor, mark=diamond*, mark size=4.1pt,
    mark options={draw=greencolor!90!black, fill=greencolor!22, line width=0.7pt}
  },
  thesispurpleseries/.style={
    thesisseries, color=purplecolor, mark=pentagon*, mark size=3.5pt,
    mark options={draw=purplecolor!90!black, fill=purplecolor!22, line width=0.7pt}
  },
}
```

## 3. 基本绘图模板

```tex
\begin{figure}[!htbp]
  \centering
  \begin{tikzpicture}
    \begin{axis}[
      thesispubaxis,
      xlabel={\heiti 时间 $t$\;\;{[s]}},
      ylabel={\heiti 压力 $P$\;\;{[kPa]}},
      legend columns=2,
      legend style={at={(0.95,0.95)}, anchor=north east},
    ]
      \addplot [thesisgrayseries, mark=none]
        table[col sep=comma, x=Time, y=Case1] {data/xxx.csv};
      \addlegendentry{\heiti 工况1}

      \addplot [thesisredseries, mark=none]
        table[col sep=comma, x=Time, y=Case2] {data/xxx.csv};
      \addlegendentry{\heiti 工况2}
    \end{axis}
  \end{tikzpicture}
  \caption{不同工况下的压力变化}
  \label{fig:pressure-comparison}
\end{figure}
```

中文标签使用 `\heiti`，变量在数学模式中，单位与数据一致。时间序列一般覆盖序列样式中的 marker，使用 `mark=none`；离散点或误差棒再启用标记。

## 4. 数据读取

绘图前核对文件存在、分隔符、列名、单位、缺失值和数值范围。CSV 用 `col sep=comma`，空格分隔数据用 `col sep=space`。表头含空格时使用花括号，如 `y={DP Exp Mean}`。

越界、`nan` 或 `inf` 应先查明来源；仅在处理规则明确时使用 `unbounded coords=discard`。`each nth point` 只用于显示降采样，必须验证峰值、转折和短时事件保留，统计仍基于完整数据。

## 5. 图例与绘制顺序

图例位置必须同时设置 `at` 与 `anchor`。若为防遮挡改变曲线绘制顺序，但图例仍需保持逻辑顺序，可使用 `legend to name`、独立 `\addlegendimage` 或命名图例。不要让后绘制的曲线无意遮住关键曲线；可调整 `z buffer`、透明度或仅改变绘制顺序。

## 6. 误差棒与模型比较

```tex
\addplot [
  thesisgrayseries,
  error bars/.cd,
  y dir=both,
  y explicit,
  error bar style={line width=0.6pt, draw=darkgray!70}
] table[
  col sep=comma,
  x=Time,
  y=Mean,
  y error=StdDevi
]{data/xxx.csv};
\addlegendentry{\heiti 实验均值}

\addplot [thesisblueseries, mark=none]
  table[col sep=comma, x=Time, y={Lee in bar}] {data/xxx.csv};
\addlegendentry{\heiti Lee 模型}

\addplot [thesisredseries, mark=none]
  table[col sep=comma, x=Time, y={LMB in bar}] {data/xxx.csv};
\addlegendentry{\heiti LMB 模型}
```

误差棒含义在图注说明。模型线和实验点使用不同视觉编码，不仅依靠颜色区分。

## 7. 多面板、共享图例与等值线

多面板共享相同的轴范围、色序和单位。共享图例可用 `legend to name=<name>` 并在面板外 `\ref{<name>}`。两个面板若分别支持不同结论，优先拆成独立图并同步更新标签与正文引用。

等值线和色带使用项目色卡或与工况曲线区分明显的连续色卡。色条必须给出量名和单位；等值线标签排列整齐并避免遮挡轨迹。背景填色降低饱和度和透明度，不压过数据曲线。

## 8. PGFPlots 技术检查

- 数据路径、列名、分隔符和单位正确。
- `\caption`、`\label`、正文 `\ref` 闭合。
- 图例顺序、色序和工况名称与全文一致。
- 降采样未丢失关键事件。
- 无 `dimension too large`、缺列、空图、图例越界或裁切。
- 在主论文中编译，并按最终页面尺寸检查字体、线宽和图文距离。
