# Pipeline Skills

> **工业管道仿真 AI Agent技能包** — 让AI助手能理解和执行管道工程仿真任务

[![GitHub](https://img.shields.io/badge/GitHub-pipeline--skills-blue)](https://github.com/chendw79/pipeline-skills)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/Powered%20by-OpenClaw-orange)](https://openclaw.ai)

## 这是什么？

一套可复用的 **AI Agent Skills**（技能包），让 Claude、Codex、OpenClaw 等 AI 编程助手能够：

- ✅ 理解管道瞬态仿真的物理原理
- ✅ 配置和运行 PipelineSim 仿真
- ✅ 分析水击、泵停等工程场景
- ✅ 生成专业仿真报告和可视化图表

## 🚀 PipelineSim Online

> **在线仿真平台已上线** 🌐 [http://43.156.28.12:8766](http://43.156.28.12:8766)
>
> 基于 FDM + RK23 (Chen et al. 2015) 水击求解器 + Colebrook 稳态管道计算

## 快速使用

### 方式一：直接复制到你的 AI Agent

```bash
# 克隆本仓库
git clone https://github.com/chendw79/pipeline-skills.git
cd pipeline-skills

# 告诉你的 AI Agent 读取 skills/ 目录下的内容
```

### 方式二：作为 OpenClaw Skill 安装

```bash
openclaw skill install https://github.com/chendw79/pipeline-skills
```

## 技能列表

| 技能文件 | 说明 | 用途 |
|---------|------|------|
| `skills/pipeline-basics.md` | 管道仿真基础概念 | 让AI理解管道仿真基本物理 |
| `skills/water-hammer.md` | 水击分析和计算 | 阀门快速关闭场景分析 |
| `skills/solver-selection.md` | 求解器选择指南 | 不同场景选最合适的求解器 |
| `skills/steady-analysis.md` | 稳态管道分析 | 压降、温降、流速计算 |
| `skills/dashboard-viz.md` | 结果可视化 | 生成交互式仿真图表 |

## 技术栈

- **仿真引擎**: [PipelineSim](https://github.com/chendw79/pipeline-sim) (Python)
- **AI Agent**: 兼容 OpenClaw / Claude Code / Codex
- **可视化**: Plotly / Matplotlib

## 路线图

- [x] 基础技能包 (5个核心技能)
- [ ] 交互式在线演示面板
- [ ] 完整文档和视频教程
- [ ] PyPI 发布

## 作者

ChenDavid — 工业仿真 + AI Agent 实践者

## License

MIT

## 📊 Pipeline Data Processing Skill

专业管道瞬态数据处理与可视化技能包，现已安装在 `skills/pipeline-data-processing.skill/`。

**能力：** KPI数据面板 / 沿程分布 / 时程曲线 / 时空热力图 / CSV导出

```bash
# 快速测试 KPI 面板
cd skills && python -c "
import sys; sys.path.append('..')
from pipeline_data_processing import PipelineDataPanel
# kpis = PipelineDataPanel.compute_kpis(result)
"
```
