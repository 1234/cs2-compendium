# Hardware

CS2 is CPU- and RAM-bottlenecked. Not GPU-bottlenecked.

This single fact is responsible for more wasted upgrade money than anything else in
the game. Players buy better GPUs, see no improvement, and don't understand why.
The GPU was never the limiting factor — the CPU was.

This section explains exactly why, what to prioritize, and what the numbers actually
look like at each tier.

---

## The bottleneck hierarchy

In CS2 at competitive settings (low/medium, 1080p or lower), the order is:

```
CPU  →  RAM  →  GPU
```

Your CPU processes the game world, physics, network, and renders the scene.
Your RAM feeds the CPU the data it needs, fast enough to keep it busy.
Your GPU rasterizes the final frame.

At competitive settings, the GPU is typically sitting at 50–70% utilization
while the CPU is at 100%. Upgrading the GPU does nothing for FPS in this state.
The CPU finishes its work, the GPU finishes its work, the CPU starts the next frame —
and the queue is empty again. The CPU is the ceiling.

---

## CPU — the actual bottleneck

### What CS2 actually uses

CS2 is heavily single-threaded for its critical workloads — game physics, network
processing, and the main render thread run on 1–2 cores at high priority.
The engine has improved multi-core utilization over time, but single-thread performance
remains the primary determinant of FPS.

**Priority order for CS2 CPU performance:**
1. **Clock speed** — boost frequency above 5.0 GHz is the single biggest driver
2. **IPC and L3 cache** — how much work each clock cycle accomplishes; larger L3 = fewer cache misses
3. **Core count** — relevant for background tasks and streaming, not for raw CS2 FPS

### AMD — 3D V-Cache changes everything

AMD's X3D processors stack additional L3 cache directly on the CPU die using 3D
packaging. In CS2, this cache advantage is decisive: the game's working set fits in
the enlarged L3, drastically reducing main memory access latency.

| CPU | Avg FPS (1080p low) | 1% low |
|---|---|---|
| Ryzen 7 9850X3D | ~705 | ~380 |
| Ryzen 7 9800X3D (prev gen) | ~669 | ~362 |
| Ryzen 7 7800X3D (older gen) | ~592 | ~318 |
| Ryzen 5 9600X | ~500+ | ~260 |

The 9850X3D (released January 2026) is the current benchmark leader for CS2,
landing roughly 5–6% above the 9800X3D in independent testing. The 9800X3D
remains the price/performance pick if you can still find one. The X3D cache
advantage is not marginal — it is the single largest per-frame improvement
available from any CPU upgrade, confirmed across multiple independent benchmark
sources.

### Intel

Intel's high-clock options remain competitive but trail the X3D cache advantage
in CS2 specifically.

| CPU | Notes |
|---|---|
| Core Ultra 9 285K | 5.7 GHz boost, strong single-thread, broadly competitive with upper mid-range AMD |
| Core i5-14600K | ~400–430 FPS avg at 1080p low (GPU-dependent), excellent price/performance |
| Core i9-13900K / 14900K | High clock, but trails X3D in CS2 despite higher raw MHz |

#### Intel-specific setting — modern hybrid scheduling

On 12th–14th gen Intel CPUs
with P-cores and E-cores, the old advice was to disable E-cores in BIOS or pin
CS2 to P-cores only. This is now outdated. The 15% figure that gets quoted
(originally from 3kliksphilip via Strafe) was measured on a 13900K with 8P+16E
in an early Win11 Thread Director environment. On 14th-gen parts with fewer
E-cores under Windows 11 24H2 with a mature Thread Director, disabling E-cores
*reduces* average FPS in CS2 — community benchmarks on the 14600K show roughly
600 avg → 500 avg when E-cores are turned off.

**Keep E-cores AND Hyperthreading enabled in BIOS.**

Use Valve's in-game setting instead. Settings → Video → Advanced Video →
*CPU core usage preference* → **Prefer Performance Cores** (added by Valve in
late 2024). This is a soft scheduler hint that asks Windows to prefer P-cores
for CS2's hot threads without starving the engine of background-thread
throughput on E-cores.

**Optional — Process Lasso Core 0 exclusion (8+ P-core SKUs only):** A small
additional gain comes from excluding Core 0 (not P-cores) from cs2.exe's affinity
via Process Lasso. Core 0 carries Windows interrupt routing and timer work;
excluding it from CS2 removes a source of frametime contention. Thour's
measurements show roughly 2.2% average and 3.9% 1%-low improvement on a
13900K (8P+16E). Exclude **Core 0 only** — not all E-cores, not all but the
P-cores.

**Do NOT apply this on 6 P-core SKUs (13600K, 14600K).** A 60-second CS2 capture
on a 14600K measured during this compendium's development showed Min FPS
collapse from 71 to 35 and AdaptiveStd worsen 9% with Core 0 excluded, while
Avg stayed flat. The Min collapse is the tell — with only 6 P-cores, taking
one off the active set leaves CS2 short of threads for its render/main loop
under load. The trade is "−1 Core 0 interrupts" versus "−1 P-core for the
renderer", and on 6P SKUs the second cost dominates. See
[exp-003](../experiments/exp-003-process-lasso-6p-core.md) for the full
capture and methodology, and [measuring](../measuring/README.md) for the
A/B protocol used to verify it.

### What not to buy for CS2

Any CPU prioritizing core count over clock speed (workstation/HEDT chips,
AMD Threadripper, older Xeon) performs poorly in CS2 relative to its price.
CS2 does not care about 16 cores. It cares about how fast the first 2 are.

---

## RAM

### Dual channel is mandatory

Running two sticks of RAM in dual channel provides roughly double the memory
bandwidth of a single stick. In CS2, this is not a subtle difference —
single-channel configurations cause significant FPS drops and frame time spikes.

**Always run 2 sticks. Always.**

### Speed matters — but how much?

RAM speed (MHz/MT/s) affects how quickly data can be fed to the CPU.
The practical FPS impact of RAM speed in CS2:

- Going from DDR4-3200 to DDR4-3600: meaningful (~10–15 FPS at the high end)
- Going from DDR5-4800 to DDR5-6000: meaningful, particularly for AMD AM5
- Going from DDR5-6000 to DDR5-7200: diminishing returns, stability risk

### AMD AM5 — DDR5-6000 CL30 is the sweet spot

This is one of the most specific and well-supported hardware recommendations
in the CS2 community. Here's why DDR5-6000 CL30 specifically:

**The 2:1 mode boundary:** AMD's Infinity Fabric (FCLK) has a 1:1 mode up to
6000MT/s and drops to 2:1 mode above it. In 2:1 mode, memory latency increases
significantly because the fabric runs at half the memory speed. DDR5-6000 is
the highest speed that stays in 1:1 mode on most AM5 systems.

**Why CL30:** At DDR5-6000, CL30 provides the best balance of speed and latency.
CL28 kits exist but are harder to stability-test and more expensive. CL32 is
slightly looser but often acceptable. CL30 is the verified sweet spot confirmed
by the overclocking and benchmarking community.

**Die recommendation:** Hynix A-die and M-die are the most stable and widely
tested ICs at DDR5-6000 CL30 for AM5. Samsung B-die DDR5 kits are less common
at this spec range and less consistent.

**Kit configuration:** 2×16GB or 2×24GB. Two sticks preferred over four —
four-stick configurations increase electrical load on the memory controller
and often require running at lower speeds for stability.

### Intel (LGA1851 / Arrow Lake, Raptor Lake)

DDR5-6000 CL30 is a solid baseline for Intel DDR5 platforms as well.
Arrow Lake's memory controller is mature as of 2025/2026 — AMD-specific
recommendations around FCLK boundaries do not apply directly, but the speed
tier is still appropriate.

For Raptor Lake (12th/13th/14th gen) on DDR4: DDR4-3600 CL16–18 is the
established sweet spot. Above DDR4-4000, stability becomes the primary concern
and FPS gains are marginal for the effort.

#### DDR4 Gear 1 vs Gear 2

The real reason DDR4-3600 wins on Raptor Lake: the integrated memory
controller runs in Gear 1 (1:1 IMC-to-memory ratio) up to roughly
DDR4-3733, and drops to Gear 2 (1:2 ratio) above it. Gear 2 halves
the memory controller frequency relative to the DIMMs, which adds latency that
the extra MT/s rarely recovers. DDR4-3600 in Gear 1 typically beats DDR4-4000
in Gear 2 by 2–4% in CS2 FPS. If you bought a DDR4-4000 XMP kit, run it at
3600 in Gear 1 rather than at rated speed in Gear 2.

The cost of Gear 2 shows up as a hard FPS ceiling that no GPU / driver / cap
optimization can move. See
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md) for a worked
case: a 14600K + RTX 4090 + DDR4-4000 at 1280×960 ceilings at ~232 FPS Avg
across six very different optimization profiles, with the CPU hotthread
stuck at 80-86 % (not 100 %) and the GPU at 25-30 % load — the canonical
memory-stall signature.

**How to verify your gear:** HWInfo64 → Memory page → look at *Memory Controller
: Memory Ratio*. 1:1 means Gear 1. 1:2 means Gear 2.

### Capacity

16GB is technically enough for CS2 on a per-frame basis — the game does not
need more than 16GB to render any single frame. But CS2 has a documented
memory leak that grows session-over-session, with users reporting commit sizes
beyond 20GB after several back-to-back matches without an engine restart.
On 16GB you will start hitting the pagefile and tanking 1%-lows once that
leak compounds.

**Practical recommendation:** 32GB if you do not restart the game between
matches. 16GB is fine if you restart cleanly between every session. 32GB is
also the right answer if you stream, run Discord/browser/OBS simultaneously,
or multitask heavily. For pure single-session CS2 performance, 16GB
dual-channel at the right speed outperforms 32GB slow single-channel in every
meaningful metric — but "single session" is the load-bearing qualifier.

---

## Intel Raptor Lake / Raptor Lake Refresh — stability

If you own a 13th or 14th gen Intel K-SKU (13600K/13700K/13900K, 14600K/14700K/14900K),
you need to address the Vmin Shift degradation issue. This is not optional and
it is not exclusive to crashing systems — the fix is preventative.

### What the issue is

Intel's Raptor Lake and Raptor Lake Refresh K-SKUs ship with a microcode
defect that causes elevated voltage requests under certain transient loads.
Over time, this degrades the silicon's minimum stable voltage (Vmin), and
the CPU progressively loses stability — manifesting as random BSODs, game
crashes, and shader-compile failures. The degradation is permanent. The
microcode fix prevents further damage; it does not restore already-degraded
silicon.

**Common symptoms:** BSOD code `0x1E` (KMODE_EXCEPTION_NOT_HANDLED),
`VIDEO_TDR_FAILURE`, CS2 crashing on map load, Unreal Engine games failing
to compile shaders.

### The fixes

Intel released microcode `0x12B` in September 2024 and a further refinement
`0x12F` in April 2025. Both are delivered via motherboard BIOS updates.

1. **Update BIOS to a version that includes microcode 0x12B or later.** Check
   your board vendor's release notes — the changelog will name the microcode
   revision. 0x12F is preferred if available for your board.
2. **Load Intel Default Settings → Performance profile** in BIOS. Do NOT use
   the vendor's "Unlimited" / "Extreme" / "Tweaker" performance profile —
   those override Intel's power and current limits and reintroduce the
   conditions that cause Vmin Shift. The Performance profile honors PL1, PL2,
   and ICCMax at Intel's specified ceilings.
3. **Verify in HWInfo64.** After applying, run HWInfo64 → Power tab. Confirm
   PL1/PL2 and IccMax match Intel's documented values for your SKU
   (e.g. 14600K: PL1 125W, PL2 181W, IccMax 200A).

Apply this even if you have not crashed yet. The microcode fix is preventative
and silicon already degraded cannot be recovered without RMA.



---

## GPU — what role it actually plays

### CS2 is CPU-bound at competitive settings

At 1080p low settings with a modern CPU, the GPU is underutilized.
This means upgrading from an RTX 3060 to an RTX 4080 produces minimal
FPS improvement — because the CPU was already delivering frames as fast
as it could process them.

### FPS tiers at 1080p low (CPU not bottlenecked)

| FPS target | GPU tier needed |
|---|---|
| 144+ FPS | Any modern GPU (RX 6600, RTX 3060 or equivalent) |
| 240+ FPS | RTX 3070 / RX 6700 XT class |
| 300+ FPS | RTX 4070 / RX 7800 XT class |
| 400+ FPS | RTX 4080 / RX 7900 XT class |
| 500+ FPS | RTX 4090 — CPU becomes the new ceiling here |

At 500+ FPS targets with a top-tier CPU, the GPU finally becomes a factor.
Below that, the CPU gets there first.

### VRAM

VRAM amount does not affect CS2 FPS or stability at any competitive resolution.
CS2 uses minimal VRAM. 6GB is sufficient at 1440p. 8GB is comfortable headroom.
Choosing a GPU for more VRAM to help CS2 is not a valid reason.

### When to upgrade your GPU for CS2

Only if one of these is true:
- Your GPU cannot maintain your target FPS even after CPU/RAM are optimized
- You play at 1440p or higher and want consistent 300+ FPS
- You want to use MSAA 4× at high refresh — that increases GPU load (8× costs
  roughly 15–18% even on top GPUs per Thour's measurements, 2×/4× is the
  pro-floor consensus)

Otherwise, GPU money is better spent on CPU, RAM, or monitor for CS2 specifically.

### VRR FPS cap — how to compute it

For G-Sync Compatible / FreeSync displays, your FPS cap needs to sit just
below the panel's max refresh so the VRR window stays active and V-Sync
never has to engage. The rule the community used in the 60–240Hz era was
"refresh minus 3" (so 237 for 240Hz, 357 for 360Hz). That fixed-FPS
heuristic does not scale cleanly to 360/480/540Hz panels — driver-side LFC
on G-Sync Compatible has looser overshoot control than native G-Sync
modules, and a 3-FPS margin is too tight at high refresh.

**Use roughly 3% below max refresh:**

| Display | Cap |
|---|---|
| 240Hz native G-Sync module | 237 |
| 240Hz G-Sync Compatible | 233 |
| 360Hz G-Sync Compatible | 349 |
| 480Hz G-Sync Compatible | 466 |
| 540Hz G-Sync Compatible | 525 |

**Reflex auto-cap formula:** When G-Sync is on, V-Sync is on at the driver
level, and a Reflex-enabled title is running, NVIDIA's driver applies its
own internal cap at:

```
Cap = Refresh − (Refresh² ÷ 3600)
```

For 240Hz that's 240 − 16 = 224 FPS. For 360Hz that's 360 − 36 = 324 FPS.
For 540Hz that's 540 − 81 = 459 FPS. Per BlurBusters G-Sync 101 and NVIDIA's
System Latency Optimization Guide, this is the lowest-latency configuration
for a Reflex title on a VRR display.

**Where to set the cap:** Prefer NVIDIA App → Graphics → Program Settings →
cs2.exe → *Max Frame Rate* over CS2's in-engine `fps_max`. Valve's `fps_max`
interacts with the Reflex SDK in ways that have produced documented frametime
spikes; a driver-side cap sidesteps that.

---

## Monitor — the most important hardware decision for CS2

The monitor is where all the hardware work becomes visible. A CPU that produces
500 FPS delivers nothing extra to a 144Hz monitor. The monitor is the final
bottleneck in the chain — and for most players, it's the most under-invested component.

### Refresh rate — the primary spec

Higher Hz = more frames displayed per second = lower display latency = fresher
image at the moment you react. The intervals:

| Hz | Frame interval |
|---|---|
| 144 | 6.94 ms |
| 240 | 4.17 ms |
| 360 | 2.78 ms |
| 480 | 2.08 ms |
| 540 | 1.85 ms |
| 600 | 1.67 ms |

**360Hz is the realistic competitive floor for players; 480–540Hz is the
realistic upper end.** 240Hz is still viable for serious play. 144Hz is
viable for ranked but outdated at the competitive level. The IEM / ESL
tournament stage runs ZOWIE XL2586X (540Hz Fast-TN, DyAc 2) through 2027,
with the [XL2586X+ at 600Hz](https://forums.guru3d.com/threads/zowie-presents-its-xl2586x-esports-monitor-with-a-staggering-600hz-refresh-rate.454896/)
in rotation. See [Fundamentals](../fundamentals/README.md) for the full Hz
analysis.

### Panel types — what actually matters for CS2

This is where most monitor guides mislead. The answer for CS2 is not the same
as the answer for a photo editor.

**TN (Twisted Nematic)**
- Response time: 1–2ms GtG — fastest pixel switching of any panel type
- Color: worst — narrow viewing angles, washed-out colors
- Why pros use it: raw speed, and ZOWIE's dominance (see below)
- Verdict: technically optimal for pure response time, but color difference vs modern IPS is meaningful

**IPS (In-Plane Switching)**
- Response time: 1–4ms GtG on modern fast-IPS panels
- Color: excellent — wide angles, accurate representation
- Verdict: best balance for competitive CS2 — modern fast-IPS at 360Hz is within
  1–2ms of TN while providing significantly better image quality

**VA (Vertical Alignment)**
- Response time: 4–6ms GtG, and dark-scene pixel transitions are noticeably slower
- The problem: dark areas of CS2 maps (smoke, shadow zones, tunnels) show smearing
  as pixels transition slowly. VA's slow dark-to-dark pixel transitions produce
  measurably longer motion blur than IPS — dark transitions being significantly slower
  than bright-to-bright, a pattern confirmed across multiple display reviewers
  (Monitors Unboxed, RTINGS).
- Verdict: **avoid for CS2.** The dark-scene smearing is a direct gameplay disadvantage.

**OLED**
- Response time: ~0.02–0.03ms GtG — effectively instant pixel switching
- Color: best available — true blacks, perfect contrast
- Refresh rate ceiling (2026): 480Hz QD-OLED ([LG 27GX790A](https://www.tomshardware.com/monitors/gaming-monitors/asus-world-first-oled-esports-monitor-can-hit-540hz-at-1080p-rog-strix-oled-model-among-four-fresh-offerings)),
  540Hz Tandem WOLED ([ASUS XG259QWPG Ace](https://rog.asus.com/articles/gaming-monitors/the-rog-strix-oled-xg259qwpg-ace-combines-oled-performance-and-tournament-grade-specs/),
  June 2026), 540Hz QD-OLED 1440p ([ASUS PG27UCDM](https://www.tomshardware.com/monitors/gaming-monitors/asus-world-first-oled-esports-monitor-can-hit-540hz-at-1080p-rog-strix-oled-model-among-four-fresh-offerings)).
  The 240Hz OLED ceiling no longer applies.
- Burn-in risk: static HUD elements (radar, HP bar, crosshair) can cause burn-in with
  extreme prolonged use — modern gaming OLEDs include pixel-shift and compensation cycles
  that reduce but don't eliminate this risk. Documented cases exist. Players putting
  4+ hours daily into CS2 at high brightness long-term should factor this in.
- VRR Flicker caveat: OLED gamma is calibrated for fixed refresh; under VRR the
  refresh varies and the calibrated curve drifts, producing visible brightness/gamma
  shift especially in dark UI areas. See
  [exp-001 § Conclusion](../experiments/exp-001-vrr-vs-fixed-refresh.md).
- Verdict: exceptional display quality with refresh rates now matching TN at 540Hz
  and approaching 600Hz Fast-TN. The case for TN over OLED in 2026 is essentially
  tournament-supply parity, not technical superiority.

### GtG vs MPRT — which response time matters

Two response time metrics appear on monitor specs. They measure different things:

**GtG (Grey-to-Grey):** How long a pixel takes to switch between two grey shades.
This is the physically meaningful metric — it directly measures how fast the display
can change what it's showing. Lower GtG = sharper motion, less ghosting.

**MPRT (Moving Picture Response Time):** Measured with a strobing backlight technique
(blur reduction mode). Represents how motion appears during backlight strobing, not
how fast pixels actually switch. A monitor can advertise 1ms MPRT while having 5ms GtG.

**For CS2: GtG is the relevant metric.** MPRT is a marketing number that doesn't
reflect real-world pixel switching performance for standard 60/144/240/360Hz use.

### Input lag

Input lag is the delay between a signal arriving at the monitor and it being
displayed. It's separate from response time and separate from refresh rate.

Most modern high-refresh gaming monitors have input lag of 1–4ms at their native
Hz in game mode. The variance between models at the same Hz is real but often
within measurement noise (~±2ms in standardized testing).

**What matters most:** Ensure game mode is enabled. Display modes like "cinema" or
"vivid" often add processing that adds 10–20ms of input lag. Game mode bypasses this.

### What pros actually use — and why

As of 2026, ZOWIE dominates the professional CS2 scene:

- ZOWIE is the official monitor partner for IEM and ESL events through 2027.
- ~90% of tracked CS2 pros use ZOWIE monitors, predominantly XL2566K (360Hz TN),
  XL2586X (540Hz Fast-TN with DyAc 2), and the [XL2586X+ at 600Hz](https://forums.guru3d.com/threads/zowie-presents-its-xl2586x-esports-monitor-with-a-staggering-600hz-refresh-rate.454896/)
  on the IEM/ESL stage rotation.
- The panel is TN — not because TN is technically superior to modern fast-IPS or
  Tandem WOLED at the same refresh, but because **ZOWIE is the tournament standard
  and pros train on what they play on**.

This is an important nuance: pro monitor choice is driven by tournament
standardization and supply, not by a unanimous technical verdict. Modern
fast-IPS at 360Hz and 540Hz Tandem WOLED ([ASUS XG259QWPG Ace](https://rog.asus.com/articles/gaming-monitors/the-rog-strix-oled-xg259qwpg-ace-combines-oled-performance-and-tournament-grade-specs/))
are technically competitive with TN for response time while providing better
image quality. The pro consensus on ZOWIE TN reflects habit, ecosystem, and
tournament supply — not a measured technical preference.

---

## The upgrade path — priority order

If you're upgrading specifically for CS2 performance, this is the order that
produces the most improvement per euro spent:

| Priority | Upgrade | Why |
|---|---|---|
| 1 | **Monitor Hz** (to 240Hz minimum) | Removes the display ceiling — all other hardware improvements are invisible below your Hz |
| 2 | **CPU** (to X3D or high-clock equivalent) | The actual bottleneck — biggest FPS gains here |
| 3 | **RAM** (to DDR5-6000 CL30 dual channel for AM5) | Feeds the CPU — significant for X3D especially |
| 4 | **Monitor Hz** (240 → 360Hz) | Only meaningful after CPU/RAM are not bottlenecking |
| 5 | **GPU** | Only if CPU/RAM are optimized and GPU is still the ceiling |

**The most common mistake:** Buying a better GPU before addressing CPU and RAM.
The GPU does nothing for CS2 FPS when the CPU is already the bottleneck.

**The second most common mistake:** Upgrading to 360Hz before the CPU can produce
360+ FPS consistently. A 360Hz monitor displaying 180 FPS gives no advantage over
a 240Hz monitor displaying the same 180 FPS.

Hardware and software must scale together. The weakest link determines the result.

---

← [Back to compendium](../README.md) → [Fundamentals](../fundamentals/README.md)
