# 水击分析与计算

让AI Agent能够分析水击场景，配置仿真参数，解读结果。

## 识别水击场景

用户描述中出现以下关键词 → 触发水击分析：

- "阀门突然关闭"、"快速关阀"
- "泵突然停机"、"断电"
- "压力冲击"、"管道振动"、"砰砰声"
- "水锤"、"water hammer"、"surge"

## 参数配置指南

### 关键输入参数

```python
pipe = Pipe(
    length=10000.0,      # 管长 (m)，典型值 5000-50000
    diameter=0.5,         # 管径 (m)，典型值 0.2-1.0
    wall_thickness=0.012, # 壁厚 (m)，典型值 0.008-0.025
)

liquid = Liquid(
    name="Crude Oil",     # 流体名称
    rho_ref=860.0,        # 密度 (kg/m³)，水=1000，原油=850-900
    bulk_modulus=1.8e9,   # 体积模量 (Pa)，水=2.2e9，原油=1.5-2.0e9
)
```

### 边界条件设置

```python
from sim.solver import flow_inlet, pressure_outlet

# 阀门关闭：流量在2秒内从0.2降至0
def inlet_Q(t):
    if t < 2.0:
        return 0.2           # 初始流量 m³/s
    elif t < 4.0:
        return 0.2 * (1 - (t - 2.0) / 2.0)  # 线性关闭
    else:
        return 0.0
```

## 结果解读

### 关键指标
- **峰值压力 (Pmax)**: 不应超过管道设计压力的1.5倍
- **Joukowsky压力**: ΔP_Jouk = ρ × a × V₀（理论最大值）
- **波传播时间**: t_wave = 2L/a
- **谷值压力**: 可能产生负压 → 管道失稳风险

### 典型输出解读

```
Water Hammer Test
────────────────────────────────────
Pipe: L=10km, D=500mm
Wave speed: a=1234.6 m/s
Joukowsky: ΔP = 2.08 MPa
Inlet peak:  3.08 MPa (Δ=2.08)  ✅ 符合理论
Error vs Joukowsky: 0.2%        ✅ 极高精度
```

## 安全检查

- Pmin < 0 → 存在负压 → 建议加装破真空阀
- Pmax > 1.5×设计压力 → 存在超压风险 → 建议加装泄压阀
- 压力波往返时间 > 阀门关闭时间 → 可能产生水击增强
