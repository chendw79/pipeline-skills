# 求解器选择指南

帮助AI Agent根据用户场景自动选择最合适的求解器。

## 决策树

```
用户描述仿真需求
        │
        ├─ 快速瞬变（秒级）
        │   ├─ 需要高精度波前 → MacCormack
        │   └─ 基准精度足够 → MOC
        │
        ├─ 慢速瞬变（分钟~小时）
        │   └─ IFDM（大时间步长，节省算力）
        │
        ├─ 极端工况（压力剧变、空化）
        │   └─ FVM（守恒格式，强激波捕捉）
        │
        └─ 不确定/首次运行
            └─ compare_solvers() 自动对比
```

## 快速调用模板

### 方案一：快速水击分析（MOC，推荐新手）

```python
from sim.fluid import Liquid
from sim.pipe import Pipe
from sim.solver import SinglePhaseTransientSolver, flow_inlet, pressure_outlet
from sim.steady import SteadyStateCalculator

pipe = Pipe(10000, 0.5, 0.012)
liquid = Liquid()

# 稳态初始化
steady = SteadyStateCalculator(pipe, liquid)
V0, P_init, T_init = steady.initialize_transient(0.2, 20.0, 1e6)

# 瞬态求解
solver = SinglePhaseTransientSolver(pipe, liquid, Nx=50)
result = solver.solve(
    t_max=30.0,
    inlet_bc=flow_inlet(lambda t: 0.2 if t < 2 else 0, lambda t: 20),
    outlet_bc=pressure_outlet(lambda t: 1e6),
    mode='A', V_initial=V0, P_initial=P_init, T_initial=20.0
)

# 输出结果
print(f"峰值压力: {result.P.max()/1e6:.2f} MPa")
```

### 方案二：高精度求解器对比

```python
from sim.solver_advanced import compare_solvers
from sim.fluid import Liquid
from sim.pipe import Pipe
from sim.solver import flow_inlet, pressure_outlet

pipe = Pipe(10000, 0.5, 0.012)
results = compare_solvers(pipe, Liquid(),
    inlet_bc=flow_inlet(lambda t: 0.2 if t<2 else 0, lambda t: 20),
    outlet_bc=pressure_outlet(lambda t: 1e6),
    mode='A', Nx=30, t_max=30, Q0=0.2, T0=20, P_out=1e6)

for name, r in results.items():
    print(f"{name}: Pmax={r.P.max()/1e6:.2f} MPa, Nt={r.Nt}")
```

### 方案三：长时间慢速瞬变（IFDM）

```python
from sim.solver_advanced import ImplicitFDMSolver

# Crank-Nicolson 隐式求解
solver = ImplicitFDMSolver(pipe, liquid, Nx=20, theta=0.5)
result = solver.solve(
    t_max=3600.0,  # 1小时仿真
    dt=5.0,         # 5秒时间步长
    ...
)
```

## 精度与性能权衡

| 场景 | 推荐求解器 | Nx推荐值 | 仿真耗时(10km/30s) |
|------|-----------|---------|-------------------|
| 快速水击检查 | MOC | 30-50 | <1s |
| 详细水击分析 | MacCormack | 50-100 | 2-5s |
| 长时段 (1h+) | IFDM | 20-40 | 5-30s |
| 极端工况 | FVM | 50-100 | 3-10s |
| 研究对比 | ALL | 30 | 10-20s |
