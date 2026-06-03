# Exp 003 — Process Lasso Core-0 exclude on a 6 P-core SKU

## Hypothesis

The widely-cited 2.2 % Avg / 3.9 % 1%-Low gain from excluding Core 0 from
cs2.exe affinity via Process Lasso (Thour's measurements on a 13900K)
should not generalize to 6 P-core SKUs (13600K / 14600K). On 8 P-core
parts the trade is "−1 Core 0 interrupt overhead" versus "−1 of 8 P-cores
for the renderer", which can tip positive. On 6 P-core parts, losing one
of six P-cores costs more than the avoided interrupts save, and the
trade should tip negative — especially on the Min and Low percentiles.

## Setup

| Item | Value |
|---|---|
| CPU | Intel Core i5-14600K (6P + 8E) |
| GPU | NVIDIA RTX 4090 |
| GPU driver | 610.47 Game Ready |
| OS | Windows 11 Pro Build 26200 (24H2) — mature Thread Director |
| Display | 270 Hz fixed, V-Sync off, G-Sync off |
| Cap | autoexec `fps_max 263` |
| Reflex | Enabled + Boost |
| HAGS | On |
| Game Mode | On |
| Process Lasso | 4.x installed, service running |

System flags constant across runs. Only cs2.exe affinity changed.

## Method

Two captures, 60 s each, F11 with CS2 focused. One variable changed:
the Process Lasso affinity rule for cs2.exe.

| Run | Affinity for cs2.exe | Verified via |
|---|---|---|
| A | Default (all 20 logical cores) | Win32 ProcessorAffinity = `0xFFFFF` |
| B | All except Core 0 (19 logical cores) | Win32 ProcessorAffinity = `0xFFFFE` (1048574) |

In Process Lasso: rule `cs2.exe` → CPU Affinity → Always → uncheck Core 0,
all others ticked.

## Results

| Metric | Run A (default) | Run B (Core 0 excluded) | Delta |
|---|---|---|---|
| Average FPS | 233 | 233 | 0 % |
| Median FPS | 248 | 246 | −1 % |
| P1 (1%-Low) | 146 | 143 | −2 % |
| P0.2 | 131 | 121 | −8 % |
| P0.1 | 118 | 96 | −19 % |
| **Min FPS** | **71** | **35** | **−51 %** |
| **AdaptiveStd** | **49** | **53** | **+9 %** |
| CPU hotthread load | 81 % | **86 %** | **+5 pp** |
| GPU load | 30 % | 25 % | −5 pp |

The CPU hotthread climbed to 86 % when Core 0 was off-limits, indicating
more pressure on the remaining 5 P-cores. Avg held flat (memory-bound
ceiling — see [exp-004](exp-004-fps-ceiling-memory-bound.md)), but every
tail metric got worse.

## Conclusion

On a 6 P-core SKU under Windows 11 24H2's Thread Director, excluding
Core 0 from cs2.exe affinity **costs frame-time stability without buying
Avg**. The Min FPS collapse from 71 to 35 is the standout — that's not
scene variance, it's the engine running out of P-core headroom for a
hot-path scheduling burst.

The Thour gain on the 13900K is real on that SKU. On 6 P-core parts the
math does not carry over.

## Recommendation update

The Compendium's Process Lasso Core-0 advice now carries an explicit
"8+ P-core SKUs only" qualifier in
[hardware/README.md § Intel-specific setting](../hardware/README.md).
On 13600K / 14600K, leave affinity at default and let Windows 11 24H2
Thread Director route threads.

## Tools used

- CapFrameX 1.8.5 (60 s captures)
- Process Lasso 4.x (Free) — affinity rule
- PowerShell `Get-Process cs2 | Select ProcessorAffinity` to verify the
  mask actually applied (Thour: don't trust the GUI alone)
- `cfx_compare_records` for the delta table

---

← [Experiments index](README.md)
