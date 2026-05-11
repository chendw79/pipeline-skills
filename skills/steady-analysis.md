# 稳态管道分析

让AI Agent能够进行管道稳态压降、温降和流速分析，快速评估管道运行状态。

## 稳态分析场景

- "这段管道的压降是多少？"
- "给定入口流量和出口压力，计算入口压力"
- "温度会下降多少？"
- "管输能力评估"

## 快速计算模板

### 压降分析

```python
from sim.fluid import Liquid
from sim.pipe import Pipe
from sim.steady import SteadyStateCalculator

pipe = Pipe(length=15000.0, diameter=0.6, wall_thickness=0.014)
liquid = Liquid(name="Crude Oil", rho_ref=860.0, bulk_modulus=1.8e9)
calc = SteadyStateCalculator(pipe, liquid)

# 给定流量0.25 m³/s，出口压力0.5 MPa
x, P = calc.pressure_profile(Q=0.25, P_outlet=0.5e6)

print(f"入口压力: {P[0]/1e6:.3f} MPa")  # 入口压力 > 出口压力
print(f"出口压力: {P[-1]/1e6:.3f} MPa")
print(f"总压降: {(P[0]-P[-1])/1e6:.3f} MPa")
```

### 温度分析

```python
x, T = calc.temperature_profile(Q=0.25, T_inlet=45.0)

print(f"入口温度: {T[0]:.1f} °C")
print(f"出口温度: {T[-1]:.1f} °C")
print(f"温降: {T[0]-T[-1]:.1f} °C")
```

### 完整分析（CLI方式）

```bash
python pipeline_sim_cli.py analyze \
    --length 15000 --diameter 0.6 \
    --flow 0.25 --p-outlet 0.5 --t-inlet 45
```

输出示例：
```
Pipeline Analysis
══════════════════════════════════════════
  Length:     15.0 km
  Diameter:   600 mm
  Flow:       250 L/s
  Velocity:   0.88 m/s
  Re:         9,126
  f:          0.0316
  P_outlet:   0.500 MPa
  P_inlet:    2.128 MPa
  T_inlet:    45.0°C
  T_outlet:   43.2°C
══════════════════════════════════════════
```

## 工程经验判据

| 参数 | 合理范围 | 异常警讯 |
|------|---------|---------|
| 流速 | 0.5-3.0 m/s | >5.0 m/s → 冲蚀风险 |
| 压降/km | 0.02-0.15 MPa/km | >0.3 MPa/km → 需要增压站 |
| 温降 | 1-5°C/km（埋地） | >10°C → 保温不足 |
| 雷诺数 | >4000 (湍流) | <2000 → 层流，需复核 |
