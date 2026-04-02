# NYCU AI Capstone Project #1 - Dataset Documentation

**Reference GitHub:** https://github.com/AndreasKuhnle/SimRLFab  
**Raw ** `20260330_033517_episode_log.txt` (4,715 rows × 24 columns)

## Dataset Summary

| Characteristic | Value |
|----------------|-------|
| **Rows (raw)** | 4,715 |
| **Features** | 18 continuous |
| **Regression target** | `order_waiting_time` (mean=383.9s, std=70.5s) |
| **Classification target** | High-wait episodes (>424.7s, **95/5 split**) |
| **Train/Test** | 80/20 (3,772 / 943 rows) |

## Data Collection

**Source:** SimRLFab job shop simulation (Python/SimPy)  
**Command:** `python run.py` (default settings)  
**Process:** 
1. Clone SimRLFab repository
2. `pip install simpy tensorforce`
3. Run simulation → generates `episode_log.txt`
4. Load → clean → engineer features → split

**Excluded files:** `machine_log.csv`, `transport_log.csv`, `order_log.csv` (no direct join keys with episode_log; future work)

## Row Semantics

Each row = **aggregated statistics over a simulation window** (`sim_step=100`):
**Not a time-series** — rows are cross-sectional observations (shuffle split valid).

## Column Descriptions

| Column | Type | Description |
|--------|------|-------------|
| `sim_time` | float | Cumulative simulation time (s) |
| `valid_actions` | int | Available dispatching choices |
| `machines_working` | float | **Fraction actively processing** |
| `machines_changeover` | float | Fraction changing tools |
| `machines_broken` | float | Fraction down |
| `processed_orders` | float | Orders completed this window |
| `transp_working` | float | Transport utilization |
| `transp_walking` | float | Transport walking time % |
| `transp_handling` | float | **Transport handling time %** |
| `selected_idle` | int | Idle machine selections |
| `threshold_waiting` | int | Waiting threshold param |
| `order_waiting_time` | float | **Regression target** |
| `alpha` | float | **RL reward weighting** |
| `inventory` | float | **Work-in-progress level** |

**Dropped:** `episode_counter`, `sim_step`, `finished_orders`

**Multicollinearity noted:**
- `transp_idle = 1 - transp_working`
- `machines_idle = 1 - machines_working - changeover - broken`

## Learning Tasks

**Regression:** Predict `order_waiting_time` from fab state  
**Classification:** `order_waiting_time > 510.18s` (95th percentile)
