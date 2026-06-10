# Exp 005: DDR4-3600 Gear 1 vs DDR4-4000 Gear 2 on 14600K (CS2)

## Hypothesis

[exp-004](exp-004-fps-ceiling-memory-bound.md) established that this rig's CS2
FPS ceiling (~232 Avg) is set by memory latency rather than by clocks,
scheduling, or cap strategy, and predicted that forcing the IMC into Gear 1 by
dropping DDR4-4000 (Gear 2) to DDR4-3600 (Gear 1) would recover 5-10% Average
FPS. This experiment tests that prediction directly: same rig, same game, with
the memory configuration as the variable under test.

If the Gear-2 latency penalty is as large as predicted, DDR4-3600 in Gear 1
(1:1 IMC ratio, lower latency despite the lower MT/s) should lift the ~232 FPS
basin by a margin on the order of the predicted 5-10%, not a fraction of a
percent.

## Setup

| Item | Value |
|---|---|
| CPU | Intel Core i5-14600K (6P + 8E, 5.3 GHz P-boost) |
| GPU | NVIDIA RTX 4090 (driver 610.47) |
| Motherboard | MSI PRO B760M-P DDR4 (MS-7E02) |
| OS | Windows 11 Pro Build 26200 (24H2) |
| Game | CS2, Deathmatch on Dust 2, 1280×960 (4:3 stretched) |
| RAM kit | G.Skill F4-4000C18-16GTZR (2×16 GB), DIMM slots A2/B2 |

Two memory configurations, one variable:

| | Baseline (exp-004 R2) | Test (this experiment) |
|---|---|---|
| Speed | DDR4-4000 | DDR4-3600 (DRAM 1795.6 MHz) |
| Gear | Gear 2 (1:2) | **Gear 1 (1:1), confirmed** |
| Primary timings | XMP, CL18 (18-22-22-42) | CL16-20-20-39, tRC 59, CR 2T |
| VDIMM | 1.35 V | 1.424 V (HWInfo VR reading) |
| CPU SA | Auto | Auto (1.208 V) |

Gear 1 verification (CPU-Z Memory tab): Memory Controller Frequency 1795.6 MHz
equals DRAM Frequency 1795.6 MHz, ratio 1:1. The exp-004 baseline ran the same
kit at DDR4-4000 in Gear 2.

The baseline timings and VDIMM in the table (CL18, 18-22-22-42, 1.35 V) are the
XMP-profile nominal values, not separately sensor-verified for that capture. The
test-side timings and the 1.424 V VDIMM are read live from CPU-Z and HWInfo.

This is a Session-B configuration: it changes frequency/gear, tightens the
primary timings, and raises VDIMM in a single step. It therefore measures the
combined config swap, not the isolated Gear effect. A gear-only run (DDR4-3600
at Auto timings and stock VDIMM) was not captured separately.

System hygiene was matched to the exp-004 R2 baseline via a pre-capture
checklist: 270 Hz, Reflex Enabled + Boost, HAGS on, G-Sync and V-Sync off,
Process Lasso default affinity, Steam/Discord/browser closed, Ultimate
Performance power plan, warm caches and shader cache.

## Method

Three 60-second CS2 captures (CapFrameX F11 with cs2.exe focused) on the Gear-1
configuration, Deathmatch on Dust 2.

Baseline reference: the exp-004 R2 run (270 Hz / cap 263 / HAGS on), CapFrameX
record `cs2.exe-2026-06-03T172736`, is the like-for-like comparison. The other
exp-004 runs (R1, R3, R4, R5) are reported as a context basin; R6 (HAGS off,
218 Avg) is excluded because it changes a different lever (see
[exp-002](exp-002-hags-rtx40-reflex.md)).

Stability gate before capture: TestMem5 anta777 Extreme, 3 cycles, 0 errors
(quick gate). WHEA-Logger checked after the test via PowerShell: 0 new events
on the test day; the only historical WHEA entries are Information-level records
from a prior session, not corrected-error warnings. No confidence pass
(y-cruncher / Karhu / 12 h) was run.

Cap note: both baseline and test captures show Max FPS far above the 263 cap
(820-937 FPS), so `fps_max 263` was not the binding constraint during these
runs. The comparison is between two effectively uncapped captures and is
internally consistent on that point.

## Results

Gear-1 test (mean of 3) vs the matched Gear-2 baseline (R2, `172736`):

| Metric | Gear-2 baseline R2 | Gear-1 test (mean of 3) | Delta |
|---|---|---|---|
| Average | 232.5 | 236.1 | +1.6% |
| Median | 247.9 | 245.9 | -0.8% |
| P1 | 146.4 | 151.0 | +3.1% |
| 1% Low (avg) | 131.5 | 134.5 | +2.3% |
| 0.2% Low (avg) | 107.0 | 107.9 | +0.8% |
| AdaptiveStd | 48.8 | 53.0 | +8.5% (n=1 baseline, directional) |

Per-capture Gear-1 values (Average / 1% Low / 0.2% Low):

| Capture | Average | 1% Low | 0.2% Low |
|---|---|---|---|
| `16939` | 236.0 | 128.4 | 96.4 |
| `161049` | 235.8 | 138.8 | 117.6 |
| `161153` | 236.6 | 136.2 | 109.7 |

Context basin: exp-004's R1-R5 runs (mixed cap/refresh/scheduling profiles, all
Gear 2) average ~231 FPS. The Gear-1 test at 236.1 sits just above that basin
(+2.2% vs the basin mean).

The two halves of this result behave oppositely. The three Gear-1 captures
agree tightly on Average (236.0 to 236.6, SD 0.42 FPS), so the +3.6 FPS over the
matched baseline is reproducible and real, just small. The same captures swing
widely on the deep lows: 0.2% Low ranges 96.4 to
117.6, a 22% spread inside one config. The baseline's 0.2% Low (107.0) lands
inside that spread. So the lows carry no separable signal: which way the
"delta" points depends on which single capture you pick. Deathmatch on Dust 2
has high spatial randomness (player count, smokes, sightlines) that dominates
the tail metrics over any memory effect. AdaptiveStd for the Gear-1 captures
(51.7 to 54.8) sits at the upper edge of the baseline basin (48.8 to 53.1), so
frametime consistency is no better and arguably marginally worse (n=1 baseline,
directional only).

Sensor-side, both baseline and test:

- GPU load 28-30%, GPU clock parked at 2535 MHz, GPU power ~110 W (TDP 450 W)
- CPU hotthread 80-81%, package power ~84 W
- CPU package temp 69 °C baseline, 71.5 °C test (Tjmax 100 °C, never approached)

The CPU-bound, GPU-starved signature from exp-004 is unchanged, as expected:
the test did not move the bottleneck class, only the memory latency within it.

## Conclusion

DDR4-3600 in Gear 1 produced a small but statistically resolvable Average gain
over DDR4-4000 in Gear 2 on this rig in CS2, far below the predicted magnitude
and not isolable to the gear change. The lows showed no separable signal.

- **Average +1.6%** vs the matched baseline (+2.2% vs the R1-R5 basin), about
  3.6 FPS out of 232. The three captures are tightly clustered (SD 0.42 FPS), so
  this gain is real and reproducible, not a noise artifact. It is also small: it
  sits below the +5% threshold pre-registered for this test, inside the +/-2%
  neutral band, below exp-004's predicted 5-10%, and below the 2-4% general
  figure in
  [hardware § DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2).
- **Median flat** (-0.8%).
- **Lows: no separable signal.** The within-config capture spread (0.2% Low
  96 to 118) is larger than the baseline delta.
- **Frametime consistency unchanged**, possibly marginally worse (AdaptiveStd
  at the top of the basin).

exp-004's qualitative finding holds and is reconfirmed: the rig is CPU/memory
bound with the GPU at 28%. Its quantitative prediction does not. Moving
DDR4-4000 Gear 2 to DDR4-3600 Gear 1 bought about 1.6% Average on this 14600K +
B760 + DDR4 setup, not 5-10%. The direction exp-004 predicted is right; the
magnitude was over-predicted, and the small gain that does exist cannot be
attributed to the gear change alone, since timings and VDIMM moved with it. On
the lows, any gear effect sits below the capture-to-capture variance of a
deathmatch scenario.

Caveats that bound this result:

- No same-config multi-capture baseline exists, so the true baseline run-to-run
  noise floor is unmeasured. The exp-004 basin spread (228 to 235) is
  between-config and between-session variance, an upper bound, not the
  replicate-noise floor. The only same-config replicate set here is the three
  Gear-1 captures (SD 0.42 FPS), which is why the Average delta resolves while
  the lows do not.
- The test conflates three changes (gear/frequency, tighter primaries, higher
  VDIMM). It cannot isolate the gear contribution alone.
- The matched baseline is a single capture (`172736`); a multi-capture
  same-config baseline would tighten the comparison.
- No PMU counters (LLC-miss rate, IPC). Like exp-004, the attribution is
  behavioral, not measured at the hardware-counter level.

The practical takeaway matches exp-004's path 2: the lever that actually moves
this ceiling is L3 cache (an X3D CPU), not DDR4 gear tuning. On this platform,
DDR4-3600 Gear 1 buys a small, real, sub-threshold gain that does not justify
the tuning effort or the elevated VDIMM on its own.

## Operator decision

The +1.6% Average is a small, real, but sub-threshold gain: the pre-registered
rule classifies the +/-2% band as neutral and prescribes rollback. The Gear-1
configuration was **retained** at operator discretion despite the rule, on
stability and preference grounds, not because the gain met the +5% keep bar. It
is stable on the quick gate, shows no measured regression, and the deep-lows
captures were no worse than baseline.

Follow-ups for anyone retaining a setup like this:

- Re-enable Memory Fast Boot (it was disabled to force retraining during the
  test).
- Run a confidence pass (y-cruncher Component Stress VST + VT3, or Karhu to
  >=10000% coverage) before treating the box as production-stable. The tightened
  primaries at 1.424 V VDIMM cleared only a 3-cycle quick gate. Until then,
  avoid unattended large file writes, since marginal memory can corrupt files
  silently before TM5 flags an error.
- VDIMM 1.424 V is at or above the conservative daily ceiling for Hynix DDR4
  (~1.40 V). Confirm the DRAM IC with Thaiphoon Burner before leaving it there
  long-term; the IC was not documented in this run.

## Recommendation update

- [exp-004 → Breaking the ceiling, path 1](exp-004-fps-ceiling-memory-bound.md#breaking-the-ceiling):
  the "5-10% expected" figure for the Gear-1 fix is not supported by this
  measurement and now points here.
- [hardware § DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2):
  the "2-4%" general figure now carries a worked-case backlink. On this specific
  rig the measured delta was about 1.6% Average, real but below that range.

## Tools used

- CapFrameX 1.8.5 (60-s captures, F11 with CS2 focused)
- `cfx_compare_records` / `cfx_get_metrics` for the delta tables
- `cfx_get_sensor_summary` (GPU load, CPU temp/power during capture)
- `cfx_get_current_system` + `cfx_get_record` (RAM speed snapshot, ReBAR / HAGS
  / Game Mode state)
- CPU-Z Memory tab (Gear 1 verification: MC ratio 1:1, primary timings)
- HWInfo64 (real VDIMM via DRAM VR VOUT, VRM and CPU temps)
- TestMem5 anta777 Extreme (3-cycle quick gate)
- PowerShell `Get-CimInstance Win32_PhysicalMemory` (DDR speed sanity; note
  `ConfiguredVoltage` reports JEDEC nominal 1.2 V, not the real VDIMM)
- PowerShell `Get-WinEvent` on `Microsoft-Windows-WHEA-Logger` (post-test WHEA
  delta)
- MSI Click BIOS 5 Overclocking Profiles (Profile 1 = `STABLE_XMP_PRE_TEST`
  rollback slot)

---

## Appendix: execution walkthrough

The procedure used to run this experiment, kept for reproducibility. The design
splits the work into a low-risk Session A (frequency/gear only) and an optional
Session B (manual timing tuning), because the operator has a history of memory
related BSODs and wanted strict one-variable-at-a-time discipline.

### Pre-flight

Memory tuning under time pressure is the main cause of bricked sessions. Do not
start tired or with less than ~4 h free. Before touching the BIOS: commit and
push all repos, pause cloud sync (OneDrive/Dropbox/Drive) and Steam downloads,
set a System Restore point (`sysdm.cpl` → System Protection → Create →
`PreMemoryTest`), keep room temperature normal, and do not hot-plug USB during
the stress phase (can trigger reboots on some B760 boards).

### Phase 0: Preparation

1. **Save the current OC profile first** (most important rollback). MSI Click
   BIOS 5 → OC → Overclocking Profiles → name it `STABLE_XMP_PRE_TEST` → save to
   Profile 1. Optionally also save to a FAT32 USB stick. This restores all
   values 1:1 in seconds, versus retyping 15+ settings from photos.
2. **Document the baseline**: DRAM Frequency, XMP status, all primary timings,
   Memory Gear / IMC:DRAM ratio, DRAM voltage, CPU SA, VCCIN. Screenshot each OC
   submenu to USB with F12 (more reliable than a phone photo).
3. **Tools**: TestMem5 from a trusted source (testmem5.com or the CoolCmd GitHub
   releases), plus the anta777 Extreme config. Thaiphoon Burner to identify the
   DRAM IC. CPU-Z for timing verification. HWInfo64 for the Gear ratio and VR
   voltages. If TM5 will not launch, disable Core Isolation → Memory Integrity
   and reboot.
4. **Recovery plan**: MSI has no automatic memory fallback. If the board does
   not POST, kill PSU power, drain with a 30 s power-button hold, then either
   pull the CR2032 for 5 min or short JBAT1 for 10 s (never with power
   connected). If Windows BSODs but the BIOS is reachable, just reload Profile 1.

### Phase 1: BIOS, Session A (frequency only)

1. Advanced mode (F7 from EZ-mode), open the OC menu.
2. Disable XMP (Intel label, not "A-XMP").
3. Disable Memory Fast Boot (forces full retraining each reboot, so a cached
   training profile cannot mask a change). Re-enable after a successful Phase 4.
4. Set DRAM Frequency to DDR4-3600. Do not touch "Memory Try It!" right below it,
   that is a preset loader that overwrites everything.
5. Leave everything else on Auto: timings, DRAM voltage (BIOS picks ~1.35 V),
   CPU SA (lands ~1.10-1.15 V on Auto), Memory Gear (BIOS picks Gear 1 at 3600).
6. Save and exit (F10). Expectation: clean boot. If it does not boot, recover
   per Phase 0 and reload Profile 1.

### Phase 2: Verification

- HWInfo64 sensors: Memory Controller Clock and Memory Clock should be roughly
  equal (~1800 MHz each) for Gear 1. If MC Clock is ~900 and Memory Clock
  ~1800, that is Gear 2.
- CPU-Z Memory tab: DRAM Frequency ~1800 MHz, ratio 1:1 = Gear 1.
- PowerShell sanity: `Get-CimInstance Win32_PhysicalMemory | Select-Object
  PartNumber, Speed, ConfiguredClockSpeed, ConfiguredVoltage`. Expect Speed and
  ConfiguredClockSpeed 3600. (ConfiguredVoltage reports JEDEC nominal, ignore
  it for VDIMM.)
- WHEA baseline: Event Viewer → System → filter Source = WHEA-Logger, or
  PowerShell `Get-WinEvent`. Record the count now to compare after Phase 3.

### Phase 3: Stability

No productive use, no git push, no backup job in this window: marginal memory
can corrupt files before TM5 detects it. Close Steam (tray Exit), Discord,
browser; pause cloud sync; disable Process Lasso (match the Phase 4 state).

- Run `TM5.exe` as admin, load the anta777 Extreme config, restart, Start.
- **Quick gate** (mandatory, ~1 to 1.5 h, 3 cycles): 0 errors = pass. Errors in
  the first 5 min = abort and roll back. Errors after 30+ min = borderline, roll
  back.
- **Confidence pass** (recommended, separate session): y-cruncher VST + VT3, or
  Karhu to >=10000%, or TM5 anta777 for 12 h. Skip only if you accept the file
  corruption residual risk.
- While TM5 runs, watch HWInfo: CPU package <90 °C, DRAM VR VOUT stable
  ~1.35 V, VRM MOS temp, and the cycle counter actually advancing.
- After TM5, recount WHEA. New WHEA warnings since Phase 2 = unstable even if
  TM5 was clean. Check Reliability Monitor (`perfmon /rel`) for new app crashes.

### Phase 4: CS2 capture

1. Warm up: 5-10 min bot match or Aim Botz. Any crashes or persistent
   microfreezes (not normal shader-compile stutter) mean back to BIOS for more
   voltage or looser timings, do not capture.
2. Match the baseline checklist exactly (270 Hz, Reflex + Boost, HAGS on, G-Sync
   and V-Sync off, Process Lasso back on with default affinity, background apps
   closed, Ultimate Performance, warm caches and shaders, same map and gamemode).
   Every unmatched item turns the FPS delta into noise.
3. Capture: CS2 focused, F11, 60 s of normal gameplay, F11 to stop. Verify
   CapFrameX recorded `cs2.exe`.
4. Compare against the exp-004 baseline `cs2.exe-2026-06-03T172736` via the
   CapFrameX MCP tools.

### Phase 5: Decision tree

- Average +5% or more, WHEA clean, TM5 clean: keep, re-enable Memory Fast Boot.
- Average within +/-2% of baseline: neutral, no benefit, roll back to Profile 1.
- Average negative, or any stability sign: immediate rollback.

Rollback is loading Profile 1 (`STABLE_XMP_PRE_TEST`) then F10, which restores
all values including sub-timings and voltages. If Profile 1 is empty, load
Optimized Defaults (F6), re-enable XMP, and set any manually changed voltages
back to Auto.

### Session B: Optional manual timings

Only after a clean Session A, if chasing additional latency.

| IC | tCL | tRCD | tRP | tRAS | Command Rate | DRAM V |
|---|---|---|---|---|---|---|
| Hynix CJR/DJR/MFR | 16 | 19 | 19 | 39 | Auto (start) | 1.40 V |
| Samsung B-die (rare in 2×16 GB) | 16 | 16 | 16 | 36 | Auto (start) | 1.45 V |

Set DRAM Timing Mode to Link, leave sub-timings on Auto. Test Command Rate 1T
only after a stable run with Auto. Raise DRAM voltage only on TM5 errors under
5 min, in 0.025 V steps to a max of 1.40 V (Hynix) or 1.45 V (B-die). Raise CPU
SA only if TM5 errors persist with correct DRAM, max 1.20 V manual. Re-run
Phase 2 + 3 + 4 per single change, never two variables at once.

In this experiment the final Session-B settings were CL16-20-20-39, tRC 59,
Command Rate 2T, at 1.424 V VDIMM (slightly above the Hynix table target).

---

← [Experiments index](README.md)
