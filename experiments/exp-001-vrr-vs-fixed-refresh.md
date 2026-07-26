# Exp 001: VRR vs fixed refresh when FPS << refresh

## Hypothesis

The canonical BlurBusters G-Sync 101 recipe (NVCP V-Sync ON + in-game V-Sync
OFF + Reflex + cap ~3% below refresh) assumes the FPS lives near refresh.
On a 540Hz OLED running a workload that produces sustained FPS well below
refresh (CS2 at competitive settings on a mid-range CPU), VRR's pacing
benefit may invert. Hypothesis: fixed refresh + matched cap is smoother than
VRR + sub-refresh cap when the FPS-to-refresh ratio is below ~0.6.

## Setup

| Item | Value |
|---|---|
| CPU | Intel Core i5-14600K (6P + 8E) |
| GPU | NVIDIA RTX 4090 |
| GPU driver | 610.47 Game Ready (May 2026) |
| RAM | 32 GB DDR4-4000 CL18 (G.Skill F4-4000C18-16GTZR), XMP active |
| Motherboard | MSI PRO B760M-P DDR4 |
| OS | Windows 11 Pro Build 26200 (24H2) |
| Primary monitor | ASUS PG27AQWP-W, 27" 1440p WOLED, 540Hz, G-Sync Compatible, DisplayPort |
| Game | Counter-Strike 2 |
| Resolution | 1280×960 (4:3 stretched) |
| Scene | 60-second deathmatch capture, consistent action level |

System flags constant across both runs: ReBAR on, HAGS on, Game Mode on,
VBS off, Defender exclusions for CS2 set, Process Lasso default affinity.

## Method

Two captures, 60 s each, F11 with CS2 focused. One variable changed:
the display/sync stack.

| Run | Display refresh | NVCP V-Sync | In-game V-Sync | G-Sync | Cap | Reflex |
|---|---|---|---|---|---|---|
| A | 540Hz | On | Off | On (Compatible, DP) | NVCP Max FR 525 | Enabled + Boost |
| B | 270Hz | Off | Off | Off | autoexec `fps_max 263` | Enabled + Boost |

Run A is the literal BlurBusters G-Sync 101 recipe for a 540Hz panel.
Run B is fixed refresh at the closest available step below sustained FPS
(the panel's pixel-clock limits at 1280×960 exposed only 240/270/540 as
refresh options).

## Results

Compiled across the relevant captures during compendium development:

| Metric | Run A (VRR @ 540) | Run B (fixed @ 270) | Delta |
|---|---|---|---|
| Average FPS | 248 | 232 | −6% |
| Median FPS | 321 | 246 | −23% |
| **P1 (1%-Low)** | **97** | **146** | **+51%** |
| P0.2 | 47 | 130 | +176% |
| P0.1 | 43 | 118 | +175% |
| Min FPS | 42 | 71 | +69% |
| **AdaptiveStd (frametime variance)** | **84** | **49** | **−41%** |

Subjectively: Run A felt visibly stuttery (micro-hitches on every fight) despite
the higher Avg/Median. Run B felt smooth and stable.

Sensor-side both runs are healthy: no power-cap hits, no thermal throttling,
CPU 5.3 GHz stable, GPU eats 25–30% load, no clock drops.

## Conclusion

VRR + cap-below-refresh **inverted** on this hardware combination. With
sustained FPS at ~30–45% of refresh and AdaptiveStd already high from the
CPU/memory-bound profile, VRR's variable-refresh stacking on top of frametime
variance produced chaotic pacing instead of glue.

Two compounding effects:

1. **OLED VRR Flicker (gamma drift, not pixel-voltage modulation).** OLED
   gamma is calibrated for a fixed refresh rate (typically the panel's
   native max). Under VRR the refresh varies, which changes subpixel
   charging time and deviates from that calibrated gamma curve. The
   visible result is brightness and gamma drift, worst in dark UI areas
   and worst when refresh swings widely. Sources: TFTCentral OLED VRR
   flicker testing; RTINGS VRR flicker research.
2. **G-Sync Compatible LFC behavior at low FPS.** When FPS drops below the
   panel's VRR floor (spec: 48Hz on this monitor; actual LFC engagement
   on G-Sync Compatible OLEDs is driver-enforced and sometimes higher
   than the spec; TFTCentral has documented engagement at ~60Hz on
   sister panels to suppress flicker), the driver's Low Framerate
   Compensation kicks in with frame-doubling. With Min dropping to 42 FPS,
   this engaged on the deepest dips and added pacing chaos.

Switching to fixed refresh at the nearest available step **below** sustained
FPS (270Hz, with FPS Median 246 and P1 146) gave the display a constant
frametime target. AdaptiveStd dropped 41%, P1 rose 51%, and the
subjective "60Hz feels smoother than 540Hz" complaint disappeared.

## Recommendation update

The compendium's V-Sync recipe is correct **when FPS lives near refresh**.
When sustained FPS is well below refresh (working heuristic: FPS-to-refresh
ratio below ~0.6) and the variance is high, prefer fixed refresh at the
nearest step that sits above 1%-Low, plus an `fps_max` matched to that
refresh minus a few frames.

**Caveat on the 0.6 threshold:** this number is derived from this single
experiment, not validated across multiple panels or workloads. Treat it as
a starting hypothesis for your own A/B test, not an established threshold.

This caveat is now reflected in [windows/README.md § When VRR inverts](../windows/README.md#when-vrr-inverts-fps-well-below-refresh).

## Tools used

- CapFrameX 1.8.5 (60 s captures, F11 hotkey)
- HWInfo64 (Memory Controller Ratio sanity, sensor cross-check)
- Claude Code MCP integration (`cfx_get_metrics`, `cfx_get_sensor_summary`,
  `cfx_compare_records`)

---

← [Experiments index](README.md)
