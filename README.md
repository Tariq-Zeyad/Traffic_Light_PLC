# Smart Traffic Light Control System (PLC)

An automated and safety-interlocked traffic signal control system for a **T-junction** (Main Street and Side Street), implemented using a **Delta PLC** and programmed in **WPLSoft** . 

The project uses a **single master cycle timer** combined with PLC comparison instructions to create a reliable, efficient, and fail-safe traffic signal sequence while minimizing memory usage and ladder logic complexity.

---

## 🚦 Features

- **Main Street Priority**
  - Green: 30 seconds
  - Yellow: 5 seconds
  - Red: 20 seconds

- **Side Street Operation**
  - Remains Red while Main Street is active
  - Green: 15 seconds
  - Yellow: 5 seconds

- **Fail-Safe Interlocking**
  - Prevents simultaneous Green signals on intersecting roads
  - Hardware/software safety protection

- **Optimized PLC Programming**
  - Single master timer (`T0`)
  - Reduced ladder complexity
  - Lower PLC memory consumption
  - Easier maintenance and troubleshooting

---

## ⚙️ System Overview

The traffic signal sequence is controlled using a single master timer:

- Timer: `T0`
- Time Base: **100 ms**
- Counting Range: **0 → 550**
- Total Cycle Time: **55 seconds**

When `T0` reaches `K550`, the timer automatically resets and starts a new cycle.

---

## 📊 Signal Timing Table

| Street | Signal | PLC Output | T0 Range | Duration |
|----------|----------|------------|------------|------------|
| Main Street | 🟢 Main-Green | `Y0` | `K0 – K300` | 30 s |
| Main Street | 🟡 Main-Yellow | `Y1` | `K300 – K350` | 5 s |
| Main Street | 🔴 Main-Red | `Y2` | `K350 – K550` | 20 s |
| Side Street | 🔴 Side-Red | `Y3` | `K0 – K350` | 35 s |
| Side Street | 🟢 Side-Green | `Y4` | `K350 – K500` | 15 s |
| Side Street | 🟡 Side-Yellow | `Y5` | `K500 – K550` | 5 s |

---

## 🔄 Traffic Sequence

### Phase 1 – Main Street Green
**Timer Range:** `0 – 300`

| Main Street | Side Street |
|------------|------------|
| 🟢 Green | 🔴 Red |

### Phase 2 – Main Street Yellow
**Timer Range:** `300 – 350`

| Main Street | Side Street |
|------------|------------|
| 🟡 Yellow | 🔴 Red |

### Phase 3 – Side Street Green
**Timer Range:** `350 – 500`

| Main Street | Side Street |
|------------|------------|
| 🔴 Red | 🟢 Green |

### Phase 4 – Side Street Yellow
**Timer Range:** `500 – 550`

| Main Street | Side Street |
|------------|------------|
| 🔴 Red | 🟡 Yellow |

---

## 🪜 Ladder Logic Architecture

### Master Cycle Timer

The timer automatically resets after reaching `K550` using a Normally Closed contact.

```text
  X0                   T0 (NC)                    [ TMR T0 K550 ]
|  |---------------| / |---------------------------(      )---
```

### Main Street Logic

#### Green Light (`Y0`)
- Active when `0 ≤ T0 < 300`
- Interlocked with Side Street Green (`Y4`)

#### Yellow Light (`Y1`)
- Active when `300 ≤ T0 < 350`

#### Red Light (`Y2`)
- Active when `350 ≤ T0 ≤ 550`

### Side Street Logic

#### Red Light (`Y3`)
- Active when `0 ≤ T0 < 350`

#### Green Light (`Y4`)
- Active when `350 ≤ T0 < 500`
- Interlocked with Main Street Green (`Y0`)

#### Yellow Light (`Y5`)
- Active when `500 ≤ T0 ≤ 550`

---

## 🔒 Safety Interlocking (Fail-Safe Design)

To ensure compliance with industrial safety standards, software interlocking is implemented:

- `Y4 (NC)` contact is placed in series with the `Y0` coil.
- `Y0 (NC)` contact is placed in series with the `Y4` coil.

```text
Main Green (Y0)
---|/| Y4 --------------------( Y0 )

Side Green (Y4)
---|/| Y0 --------------------( Y4 )
```

### Safety Benefits

- Main Street and Side Street Green lights can never be ON simultaneously.
- Additional protection exists against programming errors or unexpected runtime conditions.
- Safer operation for real-world traffic control applications.

---

### PLC Outputs

| Output | Function |
|----------|----------|
| `Y0` | Main Street Green |
| `Y1` | Main Street Yellow |
| `Y2` | Main Street Red |
| `Y3` | Side Street Red |
| `Y4` | Side Street Green |
| `Y5` | Side Street Yellow |

---

## 📈 Advantages

- Single timer architecture
- Reduced memory consumption
- Simplified ladder logic
- Easy maintenance and debugging
- Industrial-grade safety interlocking
- Reliable and continuous operation

---

## 📄 License

This project is intended for educational and industrial automation learning purposes. It can be extended with additional features such as pedestrian crossings, emergency vehicle priority, or sensor-based adaptive traffic control.
