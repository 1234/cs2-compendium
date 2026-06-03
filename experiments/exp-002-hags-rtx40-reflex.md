# Exp 002 — HAGS on vs off on RTX 4090 + Reflex

## Hypothesis

On RTX 40-series (Ada), NVIDIA's Reflex SDK uses Hardware-Accelerated GPU
Scheduling as the lowest-latency frame pacing path. Disabling HAGS should
remove Reflex's pacing leverage and degrade 1%-Low and Min FPS even if
Average FPS is largely unaffected (because the bottleneck is upstream of
the scheduler).

The pre-Ada blanket advice "HAGS off for competitive games" comes from
Pascal/Turing testing where Reflex didn't ship and HAGS produced frame-time
inconsistency. That advice should not be carried over to Ada with Reflex
enabled.

## Setup

| Item | Value |
|---|---|
| CPU | Intel Core i5-14600K |
| GPU | NVIDIA RTX 4090 |
| GPU driver | 610.47 Game Ready (May 2026) |
| Reflex | Enabled + Boost (in-game CS2 setting) |
| OS | Windows 11 Pro Build 26200 (24H2) |
| Display | 270 Hz fixed, V-Sync off everywhere, G-Sync off |
| Cap | autoexec `fps_max 263` |
| Game | Counter-Strike 2 at 1280×960 |

System flags constant: ReBAR on, Game Mode on, VBS off, Defender exclusions
set, Process Lasso default affinity. Only HAGS toggled. Reboot between runs.

## Method

Two captures, 60 s each, F11 with CS2 focused. One variable changed.

| Run | HAGS | Verified via |
|---|---|---|
| A | On | `cfx_get_current_system` → `hardwareAcceleratedGpuScheduling: enabled` |
| B | Off | `cfx_get_current_system` → `hardwareAcceleratedGpuScheduling: disabled` |

HAGS state read from `HKLM:\SYSTEM\CurrentControlSet\Control\GraphicsDrivers`
key `HwSchMode` (2 = on, 1 = off). Reboot required for toggle to take effect.

## Results

| Metric | Run A (HAGS On) | Run B (HAGS Off) | Delta |
|---|---|---|---|
| Average FPS | 232 | 218 | −6 % |
| Median FPS | 248 | 241 | −3 % |
| **P1 (1%-Low)** | **146** | **84** | **−43 %** |
| P0.2 | 130 | 77 | −41 % |
| P0.1 | 118 | 75 | −36 % |
| **Min FPS** | **71** | **25** | **−65 %** |
| Max FPS | 821 | 646 | −21 % |
| **AdaptiveStd** | **49** | **54** | **+11 %** |
| P5 FPS | 166 | 128 | −23 % |
| 1%-Low Average | 132 | 76 | −42 % |
| 1%-Low Integral | 139 | 79 | −43 % |
| CPU hotthread load | 81 % | 78 % | −3 pp |
| CPU power (avg) | 84 W | 77 W | −8 % |
| GPU load | 30 % | 26 % | −4 pp |

Notable pattern: Average dropped only 6 %, but the entire low-end tail
collapsed. P1 cut nearly in half, Min cut by two-thirds. AdaptiveStd worsened
by 11 % — exactly the chaos pattern Reflex is designed to prevent.

## Conclusion

HAGS off on RTX 40-series with Reflex active **disproportionately damages
the frametime tail**. The Average barely moves because the CPU/memory
bottleneck is upstream — but Reflex's ability to glue the bottom of the
distribution to a stable boundary disappears.

This matches NVIDIA's documented behavior: on Ada, Reflex SDK is most
effective when paired with HAGS, because the driver can deterministically
sequence frame submission and GPU work. Without HAGS, the CPU side of
Reflex's pacing logic still runs but the GPU-side queue management
degrades.

## Recommendation update

The "HAGS off for competitive games" advice is **wrong** on RTX 40 and 50
series cards running titles with Reflex (CS2 ships Reflex). On Pascal /
Turing it remains correct. On Ampere it's inconsistent — measure per
driver version.

This is reflected in [windows/README.md § HAGS](../windows/README.md).

## Tools used

- CapFrameX 1.8.5 (60 s captures)
- `cfx_get_current_system` to confirm HAGS state between runs (rules out
  "I thought I changed it" failures)
- `cfx_compare_records` for the delta table above
- Registry edit + reboot to toggle HAGS:
  `Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\GraphicsDrivers HwSchMode -Value 1` (off) or `2` (on)

---

← [Experiments index](README.md)
