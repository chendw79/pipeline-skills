---
name: pipeline-data-processing
description: >
  管道瞬态数据处理与可视化。KPI指标卡面板、沿管道里程分布曲线、
  指定位置时程曲线、多点对比、时空热力图、动态演变动画。
  触发："数据处理"、"KPI"、"沿程分布"、"时程曲线"、"数据面板"
argument-hint: "[simulation_result or data_file]"
---

# Pipeline Data Processing Skill

管道瞬态仿真数据的专业处理与可视化。

## 能力

### 📊 数据面板 (Data Panel)
- 最大/最小压力、波动幅度、入口/出口压力、总压降
- 温度极值、流速极值、密度极值
- 系统信息: 仿真时长、管线长度、节点数
- 输出: HTML面板 / 纯文本报告 / Plotly

### 📐 沿程分布 (Along-Pipe Profile)
- 单变量沿管道里程剖面 (压力/温度/流速/密度)
- 多变量归一化对比（同图显示不同变量变化趋势）
- 输出: Plotly交互式HTML / Matplotlib工程图

### 📈 时程曲线 (Time Evolution)
- 任意指定位置的压力/温度/流速随时间变化
- 多位置对比（同一变量在不同点的变化差异）
- 输出: Plotly交互式子图

### 🗺️ 时空全貌 (Heatmap)
- 距离×时间热力图，一目了然全貌
- 沿程分布动态演变动画（Play/Pause控制）

### 💾 数据导出
- CSV格式导出（沿程+时程）

## 快速开始

```python
from skills.pipeline_data_processing import (
    PipelineDataPanel, PipelineProfile,
    PipelineHeatmap, PipelineDataExport
)

# KPI面板
kpis = PipelineDataPanel.compute_kpis(result)
print(PipelineDataPanel.kpis_to_text(kpis))
html = PipelineDataPanel.kpis_to_html(kpis)

# 沿程压力分布
fig = PipelineProfile.along_pipe(result, 'P', time_idx=-1)

# 入口处时程曲线
fig = PipelineProfile.time_evolution_at_point(
    result, x_location_m=0, variables=['P', 'v']
)

# 多点对比
fig = PipelineProfile.multi_point_comparison(
    result, [0, 5000, 10000], variable='P'
)

# 时空热力图
fig = PipelineHeatmap.spacetime_heatmap(result, 'P')

# 动态演变动画
fig = PipelineHeatmap.animation_profile(result, 'P')

# 导出CSV
csv = PipelineDataExport.to_csv(result)
```

## 集成说明

该技能已集成到PipelineSim仪表板，位于:
`projects/pipeline-sim/dashboard/app.py`

数据面板（KPI卡片）、沿程分布、时程曲线直接在Dash应用中可用。
见 Dashboard > 瞬态仿真面板 > KPI区域 + 时程选择。
