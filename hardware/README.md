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
| Ryzen 7 9800X3D | ~669 | ~362 |
| Ryzen 5 9600X | ~500+ | ~260 |
| Ryzen 7 7800X3D (prev gen) | ~592 | ~318 |

The 9800X3D is the current benchmark leader for CS2. The X3D cache advantage
is not marginal — it is the single largest per-frame improvement available from
any CPU upgrade, confirmed across multiple independent benchmark sources.

### Intel

Intel's high-clock options remain competitive but trail the X3D cache advantage
in CS2 specifically.

| CPU | Notes |
|---|---|
| Core Ultra 9 285K | 5.7 GHz boost, strong single-thread, broadly competitive with upper mid-range AMD |
| Core i5-14600K | ~400–430 FPS avg at 1080p low (GPU-dependent), excellent price/performance |
| Core i9-13900K / 14900K | High clock, but trails X3D in CS2 despite higher raw MHz |

**Intel-specific setting:** On 12th–14th gen Intel CPUs with P-cores and E-cores,
set CS2's process affinity to prefer Performance cores only. Testing by 3kliksphilip
(via Strafe) documented up to 15% improvement in average and minimum frame rates
under optimal conditions — real-world gains vary by map and system configuration.
The E-cores introduce scheduling overhead without adding useful throughput for CS2's workload.

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

### Capacity

16GB is sufficient for CS2. The game does not benefit from 32GB in FPS terms.
32GB is useful if you stream, run Discord/browser/OBS simultaneously, or multitask
heavily. For pure CS2 performance, 16GB dual-channel at the right speed
outperforms 32GB slow single-channel in every meaningful metric.

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
- You want to use MSAA 8× at high refresh — that increases GPU load

Otherwise, GPU money is better spent on CPU, RAM, or monitor for CS2 specifically.

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
| 540 | 1.85 ms |

**The current competitive standard is 360Hz.** 240Hz is the minimum for serious play.
144Hz is viable for ranked but is considered outdated at the competitive level.
See [Fundamentals](../fundamentals/README.md) for the full Hz analysis.

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
- Response time: ~0.03ms GtG — effectively instant pixel switching
- Color: best available — true blacks, perfect contrast
- Refresh rate ceiling: currently 240Hz for most gaming OLEDs
- Burn-in risk: static HUD elements (radar, HP bar, crosshair) can cause burn-in with
  extreme prolonged use — modern gaming OLEDs include pixel-shift and compensation cycles
  that reduce but don't eliminate this risk. Documented cases exist. Players putting
  4+ hours daily into CS2 at high brightness long-term should factor this in.
- Verdict: exceptional display quality but capped at 240Hz currently. For players
  who prioritize refresh rate above 240Hz, OLED is not yet the answer for CS2.

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

As of 2025/2026, ZOWIE dominates the professional CS2 scene:

- ZOWIE is the official monitor partner for IEM and ESL events through 2027
- ~90% of tracked CS2 pros use ZOWIE monitors, predominantly XL2566K (360Hz TN) or XL2586X (500Hz+ TN)
- The panel is TN — not because TN is technically superior to modern fast-IPS,
  but because **ZOWIE is the tournament standard and pros train on what they play on**

This is an important nuance: pro monitor choice is partly driven by tournament
standardization, not purely by technical optimality. Modern fast-IPS at 360Hz
is competitive with TN for response time while providing better image quality.
The pro consensus on ZOWIE TN reflects habit, ecosystem, and tournament supply —
not a unanimous technical verdict against IPS.

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
