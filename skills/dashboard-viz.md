# 结果可视化与报告生成

让AI Agent能够将仿真结果转化为专业图表和工程报告。

## 快速可视化模板

### 压力沿程分布

```python
import matplotlib.pyplot as plt

# result 是 TransientResult 对象
t_idx = -1  # 最后时刻
plt.figure(figsize=(10, 5))
plt.plot(result.x/1000, result.P[t_idx]/1e6, 'b-', linewidth=2)
plt.xlabel('Distance (km)')
plt.ylabel('Pressure (MPa)')
plt.title('Pressure Profile Along Pipeline')
plt.grid(True, alpha=0.3)
plt.show()
```

### 时间历程（入口/出口压力）

```python
plt.figure(figsize=(10, 5))
plt.plot(result.t, result.P[:, 0]/1e6, 'r-', label='Inlet')
plt.plot(result.t, result.P[:, -1]/1e6, 'b-', label='Outlet')
plt.xlabel('Time (s)')
plt.ylabel('Pressure (MPa)')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()
```

### 多求解器对比

```python
from sim.solver_advanced import compare_solvers

results = compare_solvers(...)

plt.figure(figsize=(12, 6))
colors = {'MOC': '#1f77b4', 'IFDM': '#ff7f0e', 
          'MacCormack': '#2ca02c', 'FVM': '#d62728'}

for name, result in results.items():
    plt.plot(result.t, result.P[:, 0]/1e6, 
             color=colors[name], label=name, linewidth=1.5)

plt.xlabel('Time (s)')
plt.ylabel('Inlet Pressure (MPa)')
plt.legend()
plt.grid(True, alpha=0.3)
plt.title('Solver Comparison: Inlet Pressure')
plt.show()
```

## 工程报告模板

```
=== Pipeline Transient Simulation Report ===

1. Pipeline Parameters
   Length:    10.00 km
   Diameter:  500 mm
   Wall:      12 mm
   Fluid:     Water

2. Initial Conditions
   Flow:      0.200 m³/s (0.88 m/s)
   Outlet P:  1.00 MPa
   Inlet T:   20.0 °C

3. Transient Event
   2.0s: Valve closure begins
   4.0s: Valve fully closed

4. Results Summary
   Peak Pressure:  3.08 MPa (+2.08 MPa surge)
   Joukowsky ΔP:   2.08 MPa (error: 0.2%)
   Wave speed:     1,235 m/s
   Round trip:     16.2 s

5. Safety Assessment
   ⚠ Pmax (3.08 MPa) < 1.5× Design (4.50 MPa) ✅
   ✅ No negative pressure detected
   ✅ Pressure stabilizes within 30s

=== End of Report ===
```
