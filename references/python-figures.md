# Python/Matplotlib 科研绘图技术指南

使用本文件前先读取 `figure-guidance.md`。本文件只说明 Python 实现。

## 1. 技术栈与文件组织

默认采用 `pandas + numpy + matplotlib`；项目已有 seaborn 时可复用，但不要仅为主题效果增加依赖。绘图脚本、只读源数据、衍生数据、矢量输出和预览图应分别保存并能互相追踪。缺少运行库或字体时先报告，不跨后端生成替代图。

## 2. 项目色卡映射

Python 色值与 `seuthesiY.tex` 的实际 RGB/HTML 定义一致：

```python
THESIS_COLORS = {
    "darkgray": "#333333",
    "blue": "#5AAEF3",
    "red": "#E65A56",
    "green": "#1B9E77",
    "purple": "#6D61E4",
    "orange": "#FF974C",
    "pinkpurple": "#CC45F4",
    "deepblue": "#3498DB",
    "lightblue": "#AED6F1",
    "vaporred": "#E74C3C",
    "mixedgreen": "#27AE60",
}

THESIS_SERIES = [
    THESIS_COLORS["darkgray"],
    THESIS_COLORS["red"],
    THESIS_COLORS["blue"],
    THESIS_COLORS["green"],
    THESIS_COLORS["purple"],
    THESIS_COLORS["orange"],
]
```

`greencolor` 在 TeX 中的权威定义为 RGB `(27, 158, 119)`，对应 `#1B9E77`；不要沿用旧注释中的不一致色值。

## 3. Matplotlib 基础配置

项目 PGFPlots 常用尺寸为 14 cm × 8 cm，可作为论文通栏图起点，再按最终版面调整：

```python
import matplotlib as mpl
import matplotlib.pyplot as plt

CM = 1 / 2.54
mpl.rcParams.update({
    "figure.figsize": (14 * CM, 8 * CM),
    "figure.dpi": 150,
    "savefig.dpi": 600,
    "axes.linewidth": 0.8,
    "axes.edgecolor": THESIS_COLORS["darkgray"],
    "axes.labelsize": 9,
    "axes.titlesize": 10,
    "xtick.labelsize": 8,
    "ytick.labelsize": 8,
    "legend.fontsize": 8,
    "lines.linewidth": 1.8,
    "lines.solid_capstyle": "round",
    "lines.solid_joinstyle": "round",
    "xtick.direction": "in",
    "ytick.direction": "in",
    "xtick.minor.visible": True,
    "ytick.minor.visible": True,
    "axes.unicode_minus": False,
    "pdf.fonttype": 42,
    "ps.fonttype": 42,
    "svg.fonttype": "none",
})
```

中文图先查询运行环境中实际存在的中文字体，再设置 `font.sans-serif`；不得硬编码不存在的字体。保持数学符号与正文一致。

## 4. 坐标轴与图例

四周轴线保留、刻度向内，主网格使用浅灰细线，次网格默认关闭或进一步减淡。坐标标签包含量名、符号和单位。轴范围由数据和科学问题决定；截断坐标时必须明确。

时间序列默认不用密集 marker；离散实验点或黑白区分需要时再添加。图例顺序应显式控制，不让绘制顺序破坏工况逻辑。

```python
fig, ax = plt.subplots(constrained_layout=True)
ax.plot(t, y1, color=THESIS_COLORS["darkgray"], label="工况1")
ax.plot(t, y2, color=THESIS_COLORS["red"], label="工况2")
ax.set(xlabel="时间 $t$ [s]", ylabel="压力 $P$ [kPa]")
ax.grid(True, which="major", color="#D0D0D0", linewidth=0.4)
ax.grid(False, which="minor")
ax.legend(facecolor="white", edgecolor="#D0D0D0", framealpha=0.92)
```

## 5. 常见图形实现

- 时间序列：`ax.plot`；不确定度用 `fill_between`，避免透明区遮住主线。
- 实验—模型：实验数据用 `errorbar` 或散点，模型用无 marker 曲线；必要时增加残差面板。
- 分布与组间比较：优先显示原始点/分布，再叠加中心与区间，不只给柱状图。
- 相关与回归：散点、拟合线、置信区间分层绘制，报告样本数和指标。
- 热图与等值线：使用感知均匀色图和带单位的 colorbar；避免彩虹色图。
- 多面板：用 `subplot_mosaic`、`GridSpec` 或 `subplots` 建立主次层级，共享轴时统一范围和标签。

## 6. 大数据与降采样

大点云可使用 `rasterized=True`、`hexbin`、密度图或有依据的聚合。时间序列降采样需记录原始点数、输出点数和规则，并验证峰值、转折和短时事件仍被保留。统计计算使用完整数据；用于显示的降采样副本不得替代分析数据。

## 7. 导出

```python
from pathlib import Path

out = Path("figures") / "figure_name"
fig.savefig(out.with_suffix(".pdf"), bbox_inches="tight")
fig.savefig(out.with_suffix(".svg"), bbox_inches="tight")
fig.savefig(out.with_suffix(".png"), dpi=600, bbox_inches="tight")
```

需要 TIFF 时按最终尺寸和期刊要求导出。导出后打开 PDF/SVG，检查文字可选择、字体未乱码、透明度和线宽正确；再按论文实际插入尺寸查看。

## 8. Python 图形技术检查

- 脚本能从原始/衍生数据重新生成图形。
- 未在代码中隐藏数据筛选、单位换算或模拟数据。
- 字体、色值、线宽和尺寸在导出文件中生效。
- 图例、colorbar、误差棒和共享坐标轴无逻辑错误。
- PDF/SVG 保持矢量元素与可编辑文字；大点云仅将必要图层栅格化。
- 最终图已在论文插入尺寸下目视检查。
