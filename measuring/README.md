# Measuring

Most CS2 advice is recycled hearsay. Run your own measurements.

The hardware and Windows recommendations in this compendium were validated
against real captures. Where claims are non-obvious, they link to a numbered
experiment under [experiments/](../experiments/README.md) with raw data. You
can replicate the same methodology on your machine.

---

## Tools

### CapFrameX — FPS, frametime, sensors

[capframex.com](https://www.capframex.com/). Free. Requires the .NET 9 Desktop
Runtime.

What it gives you per capture:

- Average / Median / P95 / P99 / P5 / **P1 (1%-Low)** / P0.2 / P0.1 / Min / Max FPS
- **AdaptiveStd** — a unitless / ms-scale frametime variance metric. Best
  single number for "smooth feel". Lower is better. The rough bands below
  are **derived empirically from this compendium's CS2 captures on a single
  hardware combo**, not from CapFrameX documentation: under ~50 felt smooth,
  above ~70 felt chaotic. Calibrate against your own baseline.
- CPU/GPU load, power, clock, temp, VRAM during the capture window
- Persistent JSON files for cross-run comparison

Default hotkey **F11** starts and stops a capture. Default capture length is
20 s — set it to **60 s** in Settings. More samples means stable percentiles.

**Capture target gotcha:** CapFrameX auto-targets the foreground process when
F11 is pressed. If your foreground is a terminal, a browser, or `git.exe`,
that's what gets captured. Always Alt-Tab to CS2, click into the game window,
then press F11. Verify in CapFrameX → Capture History that the entry shows
`cs2.exe`, not something else.

### HWInfo64 — sensor deep-dive

[hwinfo.com](https://www.hwinfo.com/). Free.

Use when CapFrameX's built-in sensor set doesn't expose what you need:

- **Memory Controller : Memory Ratio** — 1:1 means Gear 1, 1:2 means Gear 2.
  The single most important number for Raptor Lake DDR4 owners. See
  [hardware § DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2).
- **CPU VID** per core — Vmin-Shift / degradation indicator on 13th/14th gen.
- **GPU Hot Spot vs Edge delta** — pump-out detection on aging coolers.
- **DRAM voltage** — XMP sanity.

### Claude Code MCP (optional automation)

If you use [Claude Code](https://www.anthropic.com/claude-code), CapFrameX
1.8.5+ ships an MCP server you can register once:

```
claude mcp add -s user capframex --transport http http://localhost:1337/mcp
```

The assistant can then read captures, compute metrics, compare two runs
(`cfx_compare_records`), and query the live system state
(`cfx_get_current_system`) — useful for ruling out "did the setting actually
apply" before debating its effect. Tools are read-only.

---

## Methodology — controlled A/B testing

The only way to know if a setting helps **your** system is to capture before
and after. Anecdotal "feels smoother" is not evidence. The trap most people
fall into: changing three things at once, then crediting the wrong one.

### Protocol

1. **Baseline.** Current settings as they are. 60-second capture in a
   deathmatch with consistent action. F11 with CS2 focused.
2. **One change.** Exactly one. Setting, value, flag, registry key.
3. **Re-capture.** Same map, same approximate playstyle, 60 s, CS2 focused.
4. **Compare.** Look at Avg, Median, P1, P0.2, Min, AdaptiveStd. If only Avg
   moved and the percentiles stayed put, it was probably scene variance —
   re-test.
5. **Revert if no win.** This is the step most people skip. Don't keep a
   change that didn't help; you'll lose track of which knob did what.

### What the numbers actually tell you

- **Avg up + AdaptiveStd up:** higher peaks, more chaos. Bad trade for
  competitive — chaos beats average.
- **Avg flat + P1 up + AdaptiveStd flat or down:** stabilized the tail.
  Excellent.
- **Avg up + P1 up + AdaptiveStd flat or down:** the rare clean win — keep.
- **Avg flat + P1 flat + AdaptiveStd up:** the change injected jitter without
  giving anything back. Revert.

### Run-to-run scene variance

A single 60-second capture has roughly ±5–10 % noise from scene variation
(different fights, different angles, different death timing). Treat sub-5 %
deltas as inconclusive unless two or three captures agree on direction.
P1 / P0.2 are noisier than Avg — expect ±15 % scene noise on those.

### How to tell CPU vs GPU vs memory bottleneck

CapFrameX has `cfx_analyze_bottleneck`, or read the sensor numbers directly:

| Pattern | Bottleneck |
|---|---|
| GPU load 97–99%+ | GPU-bound (rare in CS2 at competitive settings; 95% is borderline, 99% is unambiguous saturation) |
| GPU load <50%, CPU hotthread ~100% | Classic CPU-bound — IPC / clock-bound |
| GPU load <50%, **CPU hotthread plateau-ed at 75–90% (not 100%)**, no optimization moves Avg | **Memory-bound.** CPU is stalling on RAM reads. Fix: tighter timings, Gear 1, or X3D-class cache. |

The third pattern is sneaky — CPU looks "not maxed", so people blame the GPU
or driver. It's usually the L3 cache + memory latency stack.

---

## Naming convention for your own experiments

If you contribute an experiment back via PR, follow this format:

- File name: `exp-NNN-short-description.md` under `experiments/`
- Sections: **Hypothesis**, **Setup** (hardware, OS, drivers, relevant flags),
  **Method** (exactly what you A/B'd, capture length, scene), **Results**
  (data tables with at least Avg/P1/P0.2/Min/AdaptiveStd), **Conclusion**,
  **Tools used**.

Document the failures too. A null result is data.

---

← [Back to compendium](../README.md) → [Experiments](../experiments/README.md)
