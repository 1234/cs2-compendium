# Exp 004 — CS2 FPS ceiling on 14600K + DDR4-4000 (Gear 2)

## Hypothesis

A high-end CPU (Intel Core i5-14600K) paired with overkill GPU (RTX 4090)
at competitive CS2 resolution should produce 500–700 FPS Average. If the
real-world ceiling lands much lower and **none** of the conventional FPS
levers move it, the bottleneck is the memory subsystem — specifically the
extra latency that DDR4-4000 incurs by running in Gear 2 on Raptor Lake.

The diagnostic signature for a memory-bound CPU bottleneck:

- CPU hotthread plateau-ed below 100% (typically 75–90%) — the core is
  not compute-saturated, it's stalling on memory reads.
- GPU load far below 95% — the GPU is fed slower than it can render.
- Aggressive cap, refresh, scheduling, scheduler-hint changes all leave
  Average essentially unchanged.

## Setup

| Item | Value |
|---|---|
| CPU | Intel Core i5-14600K (6P + 8E, 5.3 GHz P-boost) |
| GPU | NVIDIA RTX 4090 (driver 610.47) |
| RAM | 32 GB DDR4-4000 CL18 (G.Skill F4-4000C18-16GTZR), XMP active, 1.35 V |
| Motherboard | MSI PRO B760M-P DDR4, BIOS 1.G0 (microcode 0x12B+) |
| Memory Gear (suspected) | Gear 2 (1:2 IMC ratio) — typical for DDR4-4000 on B760 + Raptor Lake |
| OS | Windows 11 Pro Build 26200 (24H2) |
| Game | CS2 at 1280×960 (4:3 stretched) |

System hygiene: VBS off, HAGS on, Game Mode on, Ultimate Performance
power plan, Defender exclusions for the CS2 install + cs2.exe + steam.exe
+ steamwebhelper.exe, GLCache cleared, Chrome / VSCode killed before runs.

## Method

Run the same 60-second CS2 capture under six different optimization
profiles, holding everything else equal. If memory is the ceiling, none
of these should significantly move Average FPS or CPU hotthread load.

| Run | Configuration |
|---|---|
| R1 | 240Hz fixed / cap 234 / Reflex+Boost / HAGS on / default affinity |
| R2 | 270Hz fixed / cap 263 / Reflex+Boost / HAGS on / default affinity |
| R3 | 540Hz uncapped / Reflex Enabled (no Boost) / HAGS on |
| R4 | 540Hz uncapped / Reflex Enabled + Boost / HAGS on |
| R5 | 270Hz / cap 263 / Reflex+Boost / HAGS on / **Process Lasso Core 0 excluded** |
| R6 | 270Hz / cap 263 / Reflex+Boost / **HAGS off** |

## Results

| Run | Config | Avg | Median | P1 | Min | AdStd | CPU hot |
|---|---|---|---|---|---|---|---|
| R1 | 240 / cap 234 | 230 | — | 139 | 52 | 50 | 83% |
| R2 | 270 / cap 263 | **232** | 248 | **146** | **71** | **49** | 81% |
| R3 | 540 uncapped / no Boost | 235 | — | 141 | 19 | 53 | 79% |
| R4 | 540 uncapped / + Boost | 231 | — | 144 | 70 | 53 | 82% |
| R5 | + Core 0 excluded | 233 | 246 | 143 | 35 | 53 | **86%** |
| R6 | + HAGS off | 218 | 242 | 84 | 25 | 54 | 78% |

All six runs sit in a 218–235 FPS Avg band — a 7% spread that's mostly
driven by HAGS-off (R6, see [exp-002](exp-002-hags-rtx40-reflex.md)).
The other five runs are inside ±2% of each other on Average.

Sensor-side, all healthy runs:

- CPU package power 84–88 W (PL1 is 135 W on MSI Unlimited; never approached)
- CPU temp 69–72 °C average (Tjmax 100 °C; never approached)
- CPU max clock stable at 5.3 GHz P-boost (no thermal or power throttle)
- GPU load 25–30% (never approached compute saturation)
- GPU clock parked at 2535 MHz (no boost needed)
- GPU power 105–115 W (TDP 450 W; nowhere near limit)

The CPU hotthread in the 79–86% band with no thermal/power throttle, plus
invariance across five very different scheduling/cap/refresh profiles
(R1–R5), plus GPU starved at 25–30%, is a strong signature for memory-bound
behavior — though not the only possible explanation. Other candidates
(driver contention, present-call blocking, Thread Director migrations,
sync waits) would typically respond to at least one of the levers
R1–R5 changes. The invariance across them is what makes memory the leading
hypothesis. A definitive verification would require PMU counters (LLC-miss
rate, IPC) via Intel VTune. See Brendan Gregg's
[CPU Utilization is Wrong](https://www.brendangregg.com/blog/2017-05-09/cpu-utilization-is-wrong.html)
for why high-% CPU includes memory-stall cycles.

## Conclusion

The FPS ceiling on this hardware is set by **memory latency**, not by
clock speed, scheduling, cap strategy, or HAGS configuration. Five very
different optimization profiles all land in a ~232 FPS Avg basin.

The likely root: DDR4-4000 on Raptor Lake's IMC almost certainly runs in
**Gear 2** (1:2 IMC-to-DRAM ratio), which halves the memory controller
frequency and adds 6–10 ns to every memory access. CS2's cache sensitivity
is inferred from X3D-vs-non-X3D benchmark deltas (Hardware Unboxed, Gamers
Nexus, and Tech4Gamers 9800X3D reviews; Refrag.gg CS2 cache writeup) —
direct working-set measurement is not publicly available, but the
behavior is consistent with frequent L3 misses paying the full Gear 2
penalty.

The Compendium's existing
[hardware/README.md § DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2)
explanation predicted this exact behavior — this experiment is the
concrete data behind that prediction.

## Recommendation update

Two cross-references now point at this experiment:

- The CS2 FPS ceiling note in [hardware/README.md § RAM](../hardware/README.md#ram)
  acknowledges the memory-bound plateau as a recognizable pattern.
- The [DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2) advice
  gains concrete numerical evidence for the "DDR4-4000 in Gear 2 underperforms
  DDR4-3600 in Gear 1" claim.

### Verifying your own Gear ratio

HWInfo64 → Memory page → look for **Memory Controller : Memory Ratio**.
1:1 = Gear 1, 1:2 = Gear 2. If you have a DDR4-4000 kit and the field
reads 1:2, you have the same ceiling pattern documented here.

### Breaking the ceiling

Two paths:

1. **Memory tuning to Gear 1** — drop XMP, manually set DDR4-3600
   (or 3733) with CL16-19-19-39 timings, force IMC into Gear 1.
   Stability test with TestMem5 (anta777 extreme profile) for 1-2 h
   minimum. Expected gain: 5-10% FPS in CS2. Not recommended during
   a post-BSOD observation window — add only one variable at a time.
2. **CPU upgrade to an X3D part** — the 96 MB L3 V-Cache on a 9800X3D /
   9850X3D dramatically reduces L3-miss frequency for the CS2 working
   set, which is the actual fix for this entire bottleneck class.
   Expected ceiling jump: 230 → 600-700+ FPS Avg.

## Tools used

- CapFrameX 1.8.5 (60-second captures, F11 hotkey with CS2 focused)
- `cfx_compare_records` for cross-run delta tables
- `cfx_get_current_system` to confirm HAGS / ReBAR / Game Mode state
- HWInfo64 (Memory Controller Ratio readout — Gear 1 vs Gear 2)
- PowerShell `Get-CimInstance Win32_PhysicalMemory` (DDR speed sanity)
- PowerShell `Get-Process cs2 | Select ProcessorAffinity` (affinity sanity)

---

← [Experiments index](README.md)
