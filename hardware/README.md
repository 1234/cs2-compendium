# Hardware

CS2 is CPU- and RAM-bottlenecked. Not GPU-bottlenecked.

This single fact is responsible for more wasted upgrade money than anything else in
the game. Players buy better GPUs, see no improvement, and don't understand why.
The GPU was never the limiting factor. The CPU was.

This section explains exactly why, what to prioritize, and what the numbers actually
look like at each tier.

---

## The bottleneck hierarchy

In CS2 at competitive settings (low/medium, 1080p or lower), the order is:

```
CPU  →  RAM  →  GPU          (storage is not in this chain at all)
```

Your CPU processes the game world, physics, network, and renders the scene.
Your RAM feeds the CPU the data it needs, fast enough to keep it busy.
Your GPU rasterizes the final frame.
Your drive does nothing once the map has loaded, which is why
[Storage](#storage-the-component-that-does-not-matter) is the one component you can
safely ignore for frame rate.

At competitive settings, the GPU sits well below saturation while one CPU thread is
pinned. How far below depends on the pairing: 50 to 70% is typical at 1080p on a
mid-range card, and it can go much lower. An RTX 4090 at 1280x960 measured **25 to 30%
load** in [exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md). Upgrading the
GPU does nothing for FPS in this state. The CPU finishes its work, the GPU finishes its
work, the CPU starts the next frame, and the queue is empty again. The CPU is the
ceiling.

---

## CPU: the actual bottleneck

### What CS2 actually uses

CS2 runs on **Source 2**, not the Source 1 engine CS:GO used. This matters when reading
hardware advice, because a large share of what circulates about "CS" and CPUs was
measured on CS:GO and was never re-tested after the engine change. Source 2 did improve
thread distribution over Source 1.

What it did not do is remove the single-thread dependency. The current picture:

- The **main simulation loop** (hit registration, movement, grenade physics) is still
  largely serial and lives on one thread. Its speed is the primary determinant of FPS.
- **Rendering** runs on its own thread, with audio and worker tasks on further threads.
- In practice CS2 **saturates roughly 2 to 3 cores** and uses another few at partial
  load, rather than spreading evenly across everything you own.

This compendium's own measurement matches that shape: in
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md), the busiest CPU thread on
a 6P+8E part sat at 79 to 86% while total package power stayed at 84 to 88 W out of a
135 W limit. One thread doing the work, the rest of the chip coasting.

**Source-quality note:** thread-level profiling of CS2 is not something the rigorous
outlets publish, so the "2 to 3 saturated cores" figure comes from community analysis
rather than from instrumented measurement
([Evetech](https://evezone.evetech.co.za/performance-pulse/cs2-cpu-usage-cores-frequency-2026/),
[Steam community reports](https://steamcommunity.com/app/730/discussions/0/594026537713459453/)).
Treat the exact core count as approximate. The load-bearing claim, that one hot thread
sets the ceiling, is directly supported by our own capture.

**Priority order for CS2 CPU performance:**
1. **Clock speed:** boost frequency above 5.0 GHz is the single biggest driver
2. **IPC and L3 cache:** how much work each clock cycle accomplishes; larger L3 = fewer cache misses
3. **Core count:** relevant for background tasks and streaming, not for raw CS2 FPS

### AMD: 3D V-Cache changes everything

AMD's X3D processors bond an extra slab of L3 cache vertically onto the compute die
through thousands of through-silicon vias, so an 8-core chiplet that normally carries
32MB of L3 ends up with 96MB
([Newegg Insider on 3D V-Cache](https://www.newegg.com/insider/3d-v-cache-explained-why-amds-x3d-cpus-dominate-pc-gaming/)).

**Why that specifically helps CS2.** Every time the CPU needs data that is not in
cache, it stalls and waits on main memory. That wait is tens of nanoseconds, and at
600 FPS a frame is only 1.7ms long, so stalls are the budget. CS2's engine leans on
the CPU for large numbers of very small, latency-sensitive calculations, which is
exactly the access pattern that punishes cache misses. Refrag's writeup puts it
directly: CS2 "thrives on fast CPU cache because of the way the game engine has been
built, and how the game relies on the CPU for a lot of very quick, small
calculations," and "having a lot of cache on the CPU means less time spent fetching
data from RAM, which means higher FPS"
([Refrag](https://refrag.gg/blog/cs2-performance-why-processor-cache-should-be-a-consideration-in-your-next-pc-build/)).
Tripling L3 does not make each core faster. It makes the core stall less often, and
in CS2 that is worth more than clock speed.

This is also why the cache route beats the memory-tuning route. If the working set
mostly lands in L3, main memory latency stops mattering as much. If it does not, you
pay full memory latency on every miss, which is the ceiling documented in
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md).

#### The current ranking, and how to read these numbers

| CPU | Cores | Max boost | L3 | CS2 standing |
|---|---|---|---|---|
| Ryzen 7 9850X3D | 8 | 5.6 GHz | 96MB | Fastest, by a small margin |
| Ryzen 7 9800X3D | 8 | 5.2 GHz | 96MB | The value pick, within a few percent |
| Ryzen 7 7800X3D | 8 | 5.0 GHz | 96MB | Still excellent, previous platform |
| Ryzen 5 9600X / 9700X | 6 / 8 | 5.4 / 5.5 GHz | 32MB | Clearly behind the X3D parts in CS2 |

The 9850X3D (January 2026) is a revision of the 9800X3D, not a new tier: same 8 cores,
same 96MB of V-Cache, and a 400 MHz higher maximum boost (5.6 GHz versus 5.2 GHz).
Independent reviews measured the gaming gain at roughly **3 to 5% depending on the
title and test scenario**, for about $20 to $30 more
([TechSpot](https://www.techspot.com/review/3082-amd-ryzen-9850x3d/),
[Tom's Hardware](https://www.tomshardware.com/pc-components/cpus/amd-ryzen-7-9850x3d-vs-ryzen-7-9800x3d),
[GamersNexus](https://gamersnexus.net/cpus/amd-ryzen-7-9850x3d-cpu-review-benchmarks-gaming-power-thermals-ft-ddr5-4800)).

**Which one to buy is genuinely arguable, and it depends on how you read a percentage.**

The case for the 9800X3D is the standard one: a few percent for real money, and the
review verdicts say the same thing, that the extra spend buys very little.

The case for the 9850X3D is specific to a game like CS2, and it is stronger than the
percentage makes it sound. Percentages compress badly at high frame rates. On a rig
already producing 600 FPS, 3 to 5% is roughly **20 to 30 more frames**, and at the top
of the stack there is no other lever that hands you that. You cannot buy it with a
better GPU, you cannot tune it out of your RAM (see the +1.6% in
[exp-005](../experiments/exp-005-ddr4-3600-gear1.md)), and there is nothing above the
X3D tier to upgrade to. For a $20 to $30 delta on a part you keep for years, "the last
20 to 30 frames that are otherwise unreachable" is a legitimate reason to pay it.

So: **9800X3D if you are value-optimising, 9850X3D if you are chasing the ceiling.**
What matters far more than the choice between them is that both sit an entire tier
above any non-X3D CPU in this game. Do not agonise over these two while running a
non-X3D chip.

**A word on the FPS numbers you will see quoted.** CS2 is rarely part of the
benchmark suites used by the outlets with the most rigorous methodology, so almost
every CS2-specific FPS figure in circulation comes from community benchmark maps with
user-submitted results, or from YouTube runs. Those numbers are not comparable across
sources: the same 9800X3D shows up at roughly 600 FPS average at 1280x960 in one
user submission and around 540 at 1920x1080 in another, while PC Guide measured 604
and 669 with PBO enabled
([Steam Workshop FPS Benchmark submissions](https://steamcommunity.com/sharedfiles/filedetails/comments/3240880604),
[PC Guide](https://www.pcguide.com/cpu/guide/best-for-cs2/)). Map, game mode, bot
count, resolution, MSAA and memory configuration each move the result more than a CPU
generation does. Treat absolute CS2 FPS numbers as valid only *within* one test on one
rig, and compare CPUs by their relative ordering, not by the raw figures.

### Intel

Intel's high-clock options remain competitive but trail the X3D cache advantage
in CS2 specifically.

| CPU | Notes |
|---|---|
| Core Ultra 9 285K | 5.7 GHz boost, strong single-thread, broadly competitive with upper mid-range AMD |
| Core i5-14600K | Strong price/performance, but heavily dependent on the memory platform it sits on. See the warning below |
| Core i9-13900K / 14900K | High clock, but trails X3D in CS2 despite higher raw MHz |

#### Do not trust a single FPS number for an Intel part

Figures around 400 to 430 FPS average at 1080p low get quoted for the 14600K. This
compendium's own measurements contradict that badly enough to be worth showing in full,
because the reason for the gap is more useful than either number.

[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md) put a **14600K with an
RTX 4090 at 1280x960 and measured 232 FPS average**, in deathmatch on Dust 2, across
six different optimization profiles that all landed in the same basin. Meanwhile a
user submission for a 14600KF at DDR4-4000 and 1024x768 reports around 662 FPS
([Steam Workshop](https://steamcommunity.com/sharedfiles/filedetails/comments/3240880604)).
Same CPU generation, nearly a 3x spread. Both numbers can be real, and the gap is
explained by two things that almost nobody states alongside their FPS figure:

1. **Benchmark map versus actual gameplay.** A static benchmark map has no opposing
   players, no smokes, no fire, no particle load and no netcode churn. Live deathmatch
   or a 5v5 on a dense map has all of it. This is the single largest source of
   disagreement between CS2 FPS numbers, and it dwarfs CPU model differences.
2. **The memory platform.** The 14600K ships on both DDR4 and DDR5 boards. On a DDR4
   board running Gear 2 it is memory-latency bound long before it runs out of clock
   speed, which is exactly the ceiling exp-004 documents. TechteamGB measured upwards
   of 35 FPS average difference on a 14600K in CS2 at low settings just from moving
   between DDR5 speeds
   ([TechteamGB](https://techteamgb.co.uk/2024/02/23/14600k-ram-speed-testing-for-gaming-6000mt-s-vs-5600-vs-5200-vs-4800/)).

**Practical consequence.** If you are on a 14600K and sitting around 200 to 250 FPS in
real matches, that is not necessarily a broken system, and it is not comparable to a
600 FPS benchmark-map screenshot. Check your memory ratio first
([see below](#the-11-ratio-why-the-faster-kit-can-be-the-slower-kit)), and understand
that on a DDR4 board the remaining headroom is small: exp-005 recovered only +1.6% by
forcing Gear 1. The lever that actually moves this class of ceiling is L3 cache, which
means an X3D part on a new platform, not more tuning on the old one.

#### Intel-specific setting: modern hybrid scheduling

On 12th–14th gen Intel CPUs
with P-cores and E-cores, the old advice was to disable E-cores in BIOS or pin
CS2 to P-cores only. This is now outdated. The 15% figure that gets quoted
(originally from 3kliksphilip via Strafe) was measured on a 13900K with 8P+16E
in an early Win11 Thread Director environment. On 14th-gen parts with fewer
E-cores under Windows 11 24H2 with a mature Thread Director, disabling E-cores
*reduces* average FPS in CS2. Community benchmarks on the 14600K show roughly
600 avg → 500 avg when E-cores are turned off.

**Keep E-cores AND Hyperthreading enabled in BIOS.**

Use Valve's in-game setting instead. It lives under
**Settings → Game → *CPU core usage preference* → Prefer Performance Cores**.

Note the location: it is in the **Game** tab. It is not under Video or Advanced Video,
which is where a lot of guides send you and the usual reason people conclude the option
does not exist any more. It also only appears on hybrid Intel CPUs (12th to 14th gen,
P-cores plus E-cores), so it is invisible on AMD and on non-hybrid parts.

It is a soft scheduler hint that asks Windows to prefer P-cores for CS2's hot threads
without starving the engine of background-thread throughput on E-cores. 3kliksphilip
benchmarked the setting on a 13900K and measured up to **10% higher FPS** from choosing
Prefer Performance Cores ([Thour](https://x.com/ThourCS2/status/1870328503033287158)).
Disabling E-cores in BIOS is the worse version of the same idea, because it also splits
up L2 cache resources and removes background-thread capacity, which is why it can end
up net negative, particularly on 6 P-core parts.

**Optional, Process Lasso Core 0 exclusion (8+ P-core SKUs only):** A small
additional gain comes from excluding Core 0 (not P-cores) from cs2.exe's affinity
via Process Lasso. Core 0 carries Windows interrupt routing and timer work;
excluding it from CS2 removes a source of frametime contention. Thour's
measurements show roughly 2.2% average and 3.9% 1%-low improvement on a
13900K (8P+16E). Exclude **Core 0 only**. Not all E-cores, not all but the
P-cores.

**Do NOT apply this on 6 P-core SKUs (13600K, 14600K).** A 60-second CS2 capture
on a 14600K measured during this compendium's development showed Min FPS
collapse from 71 to 35 and AdaptiveStd worsen 9% with Core 0 excluded, while
Avg stayed flat. The Min collapse is the tell, with only 6 P-cores, taking
one off the active set leaves CS2 short of threads for its render/main loop
under load. The trade is "−1 Core 0 interrupts" versus "−1 P-core for the
renderer", and on 6P SKUs the second cost dominates. See
[exp-003](../experiments/exp-003-process-lasso-6p-core.md) for the full
capture and methodology, and [measuring](../measuring/README.md) for the
A/B protocol used to verify it.

### What not to buy for CS2

Any CPU prioritizing core count over clock speed and cache (workstation/HEDT chips,
AMD Threadripper, older Xeon) performs poorly in CS2 relative to its price. Sixteen
slow cores lose to six fast ones here, because the cores past the handful CS2 actually
saturates contribute nothing to your frame rate.

Six to eight strong cores is the right shape for this game. Beyond that you are buying
capability CS2 will not use, and the money belongs in cache and memory latency instead.

---

## RAM

### Dual channel is mandatory

Running two sticks of RAM in dual channel provides roughly double the memory
bandwidth of a single stick. In CS2, this is not a subtle difference.
Single-channel configurations cause significant FPS drops and frame time spikes.

**Always run 2 sticks. Always.**

### Speed matters, but how much?

RAM speed (MHz/MT/s) affects how quickly data can be fed to the CPU.
The practical FPS impact of RAM speed in CS2:

- Going from DDR4-3200 to DDR4-3600: meaningful (~10–15 FPS at the high end)
- Going from DDR5-4800 to DDR5-6000: meaningful, particularly for AMD AM5
- Going from DDR5-6000 to DDR5-7200: not just diminishing returns. On AM5 this can be
  a **net loss**, because of the ratio boundary explained next.

The last point is the one that costs people money, so it gets its own section.

### The 1:1 ratio: why the faster kit can be the slower kit

This is the single most misunderstood thing about buying RAM for a competitive
shooter, and it is the reason a DDR5-8000 kit can lose to a DDR5-6000 kit in CS2.

**The MT/s number on the box is bandwidth. CS2 is bound by latency.** Bandwidth is
how much data per second the memory can move. Latency is how long a single request
takes to come back. CS2's engine issues large numbers of small, scattered requests, so
what it feels is the round trip, not the throughput. A kit can raise the first number
and still make the second one worse. That is not a paradox, it is what happens when
you cross a ratio boundary.

#### Three clocks, not one

On AM5 there are three separate frequencies, and confusing them is where most bad
advice comes from ([The Overclocking Page](https://theoverclockingpage.com/2025/06/08/fclk-uclk-io-die-fabric-what-do-these-terms-mean-in-ryzen-9000-check-out-our-guide/?lang=en)):

| Clock | What it is |
|---|---|
| **MEMCLK** | The memory itself. DDR is double data rate, so DDR5-6000 means MEMCLK 3000 MHz. |
| **UCLK** | The CPU's memory controller (UMC). It runs either at MEMCLK (1:1) or at half MEMCLK (1:2). |
| **FCLK** | The Infinity Fabric that ties the package together. On AM5 this is decoupled from memory. |

**The ratio people mean when they say "1:1" is UCLK:MEMCLK, not FCLK.** This is worth
being pedantic about, because the wrong version is everywhere. Tying the boundary to the
Infinity Fabric is how it worked on AM4 / Zen 3, where FCLK did have to be kept in step
with memory. On Zen 4 and
Zen 5 the fabric is decoupled: leave FCLK on Auto, where it lands around 2000 MHz, and
it is no longer the thing you are trading against. The memory controller is what falls
out of step above the boundary, and that is what costs you.

#### What 1:2 actually costs

In 1:1 the controller ticks once per memory tick and data flows straight through. In
1:2 the controller runs at half memory speed, so the two clock domains no longer line
up. Every transfer has to be buffered briefly while it is handed between domains, and
that synchronisation is added latency on every access. Nothing about your kit got
slower; the path to it got longer.

That is the whole trap. Going from DDR5-6000 to DDR5-7200 buys you 20% more bandwidth
and, if it drops you into 1:2, hands back more than that in latency on a workload that
only cares about latency.

#### Where the boundary sits on AM5

| Configuration | UCLK:MEMCLK | Practical read |
|---|---|---|
| DDR5-6000 | 1:1 on Auto | The safe default. What you buy if you do not want to tune. |
| DDR5-6400 | 1:1, but usually needs setting manually | The enthusiast pick. Most chips do it, not all. |
| DDR5-6600 | 1:1 only on lucky samples | Silicon lottery. Do not plan around it. |
| DDR5-7200 to 7600 | 1:2 | Higher on paper, typically worse latency. Avoid for CS2. |
| DDR5-8000+ | 1:2 | Enough raw bandwidth to start overcoming the penalty again, but tuning-heavy and stability-sensitive. |

By default, Zen 5 switches to 1:2 for anything above 6000 MT/s, so a 6400 kit that
you simply enable EXPO on may well be running 1:2 and performing worse than a 6000 kit
next to it. With UCLK set equal to MEMCLK manually, roughly 6400 MT/s is the general
stable ceiling, "with only a few samples reaching 6600 MT/s"
([The Overclocking Page](https://theoverclockingpage.com/2025/06/08/fclk-uclk-io-die-fabric-what-do-these-terms-mean-in-ryzen-9000-check-out-our-guide/?lang=en)).
TechPowerUp's Zen 5 memory scaling study is the most systematic public dataset on this
and reaches the same practical conclusion, that 6000 to 6400 in 1:1 is the sensible
target and that it takes around DDR5-8000 before 1:2 claws the latency penalty back
([TechPowerUp](https://www.techpowerup.com/review/ddr5-memory-performance-scaling-with-amd-zen-5/)).

**So the buying rule is simple:** do not buy a kit faster than you can run in 1:1. A
6000 CL30 kit you leave on EXPO beats a 7200 kit you never tune, and it beats a 6400
kit that silently landed in 1:2.

#### How to check which mode you are in

Do this before you conclude anything about your memory. All three tools work:

- **HWInfo64** → Memory page → *Memory Controller : Memory Ratio*. 1:1 or 1:2.
- **CPU-Z** → Memory tab → compare *Memory Controller Frequency* against
  *DRAM Frequency*. Equal means 1:1; half means 1:2.
- **AMD Ryzen Master** → shows the UCLK and MEMCLK values directly.

To measure whether a change actually helped, use AIDA64's Cache & Memory benchmark and
watch the **DRAM Latency** figure in nanoseconds, cold after a reboot. That number
moves when the ratio changes, and it moves before your FPS does.

#### An honest calibration on how much this is worth

The ratio is real and the direction is not in doubt. The size of the payoff in actual
CS2 frames is easy to oversell, and this compendium has the receipt for that.
[exp-005](../experiments/exp-005-ddr4-3600-gear1.md) tested the equivalent change on
the Intel DDR4 side, moving from DDR4-4000 in Gear 2 to DDR4-3600 in Gear 1. Memory
latency improved measurably, from roughly 62 to 65 ns down to 59.1 ns cold, and CS2
Average FPS moved **+1.6%**, not the 5 to 10% that had been predicted.

Read that as the calibration it is. Getting the ratio right when you buy is free, so
do it. Chasing it afterwards with hours of timing tuning and elevated DIMM voltage
usually is not worth it, and the lever that actually moves this ceiling is L3 cache,
which means the CPU.

### AMD AM5: DDR5-6000 CL30 is the sweet spot

With the ratio understood, the standard recommendation follows directly: DDR5-6000
CL30 is the fastest speed that runs 1:1 on Auto on essentially every AM5 chip, which
is why it has been the community default since AM5 launched.

**Why CL30:** At DDR5-6000, CL30 provides the best balance of speed and latency.
CL28 kits exist but are harder to stability-test and more expensive. CL32 is
slightly looser but often acceptable. CL30 is the verified sweet spot confirmed
by the overclocking and benchmarking community.

**Die recommendation:** Hynix A-die and M-die are the most stable and widely
tested ICs at DDR5-6000 CL30 for AM5. Samsung B-die DDR5 kits are less common
at this spec range and less consistent.

**Kit configuration:** 2×16GB or 2×24GB. Two sticks preferred over four.
Four-stick configurations increase electrical load on the memory controller
and often require running at lower speeds for stability.

### Intel (LGA1851 / Arrow Lake, Raptor Lake)

DDR5-6000 CL30 is a solid baseline for Intel DDR5 platforms as well.
Arrow Lake's memory controller is mature as of 2025/2026. AMD-specific
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
3600 in Gear 1 rather than at rated speed in Gear 2. One worked case on a
14600K + B760 measured about 1.6% Average (gear plus tightened timings
combined), at or just below the low end of that range; see
[exp-005](../experiments/exp-005-ddr4-3600-gear1.md).

The cost of Gear 2 shows up as a hard FPS ceiling that no GPU / driver / cap
optimization can move. See
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md) for a worked
case: a 14600K + RTX 4090 + DDR4-4000 at 1280×960 ceilings at ~232 FPS Avg
across six very different optimization profiles, with the CPU hotthread
stuck at 80-86 % (not 100 %) and the GPU at 25-30 % load, the canonical
memory-stall signature.

**How to verify your gear:** HWInfo64 → Memory page → look at *Memory Controller
: Memory Ratio*. 1:1 means Gear 1. 1:2 means Gear 2.

### Capacity

**First, separate two things that both get called "a RAM problem".** Capacity and
latency are different failures with different symptoms and different fixes:

| Problem | Symptom | Fix |
|---|---|---|
| Not enough **capacity** | Fine at first, degrades over a long session, 1% lows collapse, eventually crashes | More GB, or restart the game |
| Bad memory **latency** (wrong ratio, DDR4 Gear 2) | Bad from the first round, a hard FPS ceiling that no setting moves | Correct ratio, or a CPU with more L3 |

If your frame rate is capped from the moment you load in, adding capacity will do
nothing. That is the latency case, documented in
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md).

**Capacity itself.** 16GB is enough to render any single CS2 frame. The problem is that
CS2 does not hold still: it has a long-standing, still-unfixed memory leak that grows
across a session. Users report commit sizes past 20GB after back-to-back matches
without restarting the engine, and after a September 2025 update players reported CS2
climbing beyond 20,000 MB until the system hit 100% and the game crashed
([Steam community reports](https://steamcommunity.com/app/730/discussions/0/595160389826651489/),
[GitHub issue](https://github.com/ValveSoftware/csgo-osx-linux/issues/3925)). On 16GB
you reach the pagefile sooner and your 1% lows fall apart when you get there.

**The honest caveat on 32GB:** it buys time, it does not buy immunity. There are
reports of the leak consuming 32GB as well. This is a bug, not a capacity requirement,
so treat 32GB as headroom that delays the problem rather than a fix for it.

**Practical recommendation:** 32GB if you do not restart the game between matches, and
also if you stream or run Discord, a browser and OBS alongside. 16GB is workable if you
restart cleanly between sessions. Restarting CS2 between long sessions is the actual
remedy at any capacity. And for pure single-session performance, 16GB dual-channel at
the right speed and ratio beats 32GB configured badly, every time.

---

## Storage: the component that does not matter

For CS2 frame rate, storage is irrelevant. Not "a minor factor". Irrelevant.

SATA SSD, NVMe Gen 3, Gen 4, Gen 5, it makes no difference to your average FPS, your
1% lows, or your frametime consistency in a live round. This is the one place in a
gaming PC where spending more buys you nothing measurable for this specific game.

**Why, mechanically.** A drive only participates while assets are being loaded. Maps,
textures, models and sounds are read once, decompressed, and placed into system RAM
and VRAM. From that point the render loop reads from RAM and VRAM, and the drive is
idle. Frame generation never touches it. As long as storage can fill memory during the
load screen, its speed has no path to influence what happens inside a 1.7ms frame
([Switchblade Gaming](https://www.switchbladegaming.com/game-settings/ssd-vs-hdd-gaming/),
[swap.gg](https://swap.gg/blog/is-cs2-cpu-or-gpu-intensive)). This is also consistent
with what the bottleneck signature in
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md) looks like: the ceiling
sat in the CPU and memory subsystem, with no storage involvement anywhere in the
frametime picture.

**What storage does buy you.** Real, but not FPS:

- Map load times and server connect time
- First-launch shader compilation, and rebuilds after a driver update or a cleared GLCache
- Alt-tabbing, Steam updates, and general desktop responsiveness

Going from a hard drive to any SSD is a large quality-of-life upgrade. Going from a
SATA SSD to Gen 5 NVMe is, for CS2, a benchmark number you will never feel in a match.

**The one exception, and it is really a RAM problem.** If the system runs out of
physical memory, Windows starts paging to disk, and at that moment your drive becomes
part of your frametime. This is exactly the failure mode described under
[Capacity](#capacity): CS2's session-over-session memory growth on a 16GB machine
eventually pushes you into the pagefile, and 1% lows fall apart. A faster SSD makes
that collapse marginally less catastrophic. It does not fix it. The fix is enough RAM,
or restarting the game. Do not buy a faster drive to paper over a memory shortage.

**Practical recommendation:** any decent SSD with sane sustained write behaviour. Put
CS2 wherever is convenient. Then spend the money you did not spend on Gen 5 on the
CPU, the RAM, or the monitor, in that order.

---

## Intel Raptor Lake / Raptor Lake Refresh: stability

If you own a 13th or 14th gen Intel K-SKU (13600K/13700K/13900K, 14600K/14700K/14900K),
you need to address the Vmin Shift degradation issue. This is not optional and
it is not exclusive to crashing systems. The fix is preventative.

### What the issue is

Intel's Raptor Lake and Raptor Lake Refresh K-SKUs ship with a microcode
defect that causes elevated voltage requests under certain transient loads.
Over time, this degrades the silicon's minimum stable voltage (Vmin), and
the CPU progressively loses stability, manifesting as random BSODs, game
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
   your board vendor's release notes. The changelog will name the microcode
   revision. 0x12F is preferred if available for your board.
2. **Load Intel Default Settings → Performance profile** in BIOS. Do NOT use
   the vendor's "Unlimited" / "Extreme" / "Tweaker" performance profile.
   Those override Intel's power and current limits and reintroduce the
   conditions that cause Vmin Shift. The Performance profile honors PL1, PL2,
   and ICCMax at Intel's specified ceilings.
3. **Verify in HWInfo64.** After applying, run HWInfo64 → Power tab. Confirm
   PL1/PL2 and IccMax match Intel's documented values for your SKU
   (e.g. 14600K: PL1 125W, PL2 181W, IccMax 200A).

Apply this even if you have not crashed yet. The microcode fix is preventative
and silicon already degraded cannot be recovered without RMA.



---

## GPU: what role it actually plays

### CS2 is CPU-bound at competitive settings

At competitive resolution and settings, the GPU spends most of its time waiting. This
compendium has a direct measurement of how extreme that is:
[exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md) ran an **RTX 4090 at
1280x960 and logged 25 to 30% GPU load**, with the GPU clock parked at 2535 MHz and
drawing 105 to 115 W out of a 450 W budget. The card was never asked to work. Six very
different optimization profiles all landed in the same ~232 FPS basin, because the
limit was upstream in the CPU and memory subsystem.

That is the whole story of GPUs in CS2: **the GPU's cost is masked as long as the CPU
gets there first.** Thour states the consequence plainly after benchmarking MSAA: it
"is as performance-intensive as ever, but since we're CPU-limited, it's largely masked
at lower values like 2x and 4x"
([Thour](https://x.com/ThourCS2/status/1810331191184117976?lang=en)).

#### Why most CS2 "GPU benchmarks" are useless

Be careful with GPU comparison tables for this game. Published figures put an RTX 5090
at around 432 FPS at 1080p, an RTX 5080 at 324, and an RTX 5070 Ti at 316
([Evetech](https://evezone.evetech.co.za/performance-pulse/cs2-fps-tier-list-rtx-5050-5090)).
A 5090 has multiple times the raster throughput of a 5070 Ti. If it only produces 37%
more frames, the test was not measuring the graphics cards. It was measuring the CPU
in that test rig, and every card in the table is standing in the same queue.

You can spot this pattern in the wild: identical FPS across every settings preset is
the classic report from a player with a top-tier GPU and a mismatched CPU, and it is
exactly what CS2 does when the GPU is irrelevant to the outcome.

### The two levers that create real GPU load

Only two things reliably move work back onto the graphics card in CS2.

**1. Resolution.** More pixels is more raster work, and nothing else in the frame
changes. The scaling is not linear with pixel count, because at low resolution you were
never GPU-bound to begin with. One test run at maximum settings measured 252 FPS at
1080p, 238 at 1440p (about 6% down), and 180 at 4K (about 29% down)
([swap.gg](https://swap.gg/blog/is-cs2-cpu-or-gpu-intensive)). Note the shape: 1080p to
1440p barely registers because the CPU is still the wall, then 4K finally makes the GPU
the wall. Most competitive players run *below* 1080p (1280x960 stretched and similar),
which pushes the GPU even further out of the picture.

**2. MSAA.** This is the expensive one, and the setting most likely to make your GPU
the limit. Thour's measurements show how the cost depends entirely on which card you
have. Same game, same 8x MSAA setting, two different rigs:

| Rig | MSAA off | MSAA 8x | Cost |
|---|---|---|---|
| Slower rig | 229.7 FPS | 162.1 FPS | **-29.4%** |
| Faster rig | 619.7 FPS | 547.9 FPS | **-11.6%** |

On the slower machine MSAA 8x takes nearly a third of the frames. On the faster one the
same setting costs well under half that, proportionally, because there is headroom to
absorb it ([Thour](https://x.com/ThourCS2/status/1810331191184117976?lang=en)). Read
that as two whole systems rather than a clean GPU swap, since the rigs differ in more
than the graphics card, but the direction is the point: the more headroom you have, the
less MSAA costs you.

A separate Thour run shows the full curve on one machine, at high settings with Reflex
disabled on Inferno: Off 319.4, CMAA2 306.5, 2x 297.3, 4x 275.2, 8x 224.1
([Thour](https://x.com/ThourCS2/status/1698277065110286845?lang=en)). Note that 2x is
nearly free and 8x is not.

#### 8x MSAA is not just a luxury setting

It is tempting to file high MSAA under "things pros turn off for frames". That is not
what is happening. **donk plays 1280x960 stretched with 8x MSAA**, alongside Low model,
texture, shader and particle detail
([cs.money](https://cs.money/blog/esports/donks-cs2-settings-video-crosshair-mouse/),
[BLAST.tv](https://blast.tv/article/donk-cs2-settings)). Everything cheap is off and
the expensive anti-aliasing is maxed. That combination is deliberate.

The reason is resolution. Competitive players run low resolutions, often 4:3 stretched,
on monitors that are physically large and natively much higher resolution. Every edge
in the scene is then reconstructed across more physical pixels than it was rendered
for, and aliasing that would be invisible at native 1440p becomes a shimmering,
pixelated mess on player silhouettes at distance. MSAA is what buys that clarity back,
and at 8x it buys a lot of it.

So treat AA as a real requirement rather than a slider to zero:

- If you play stretched or below native resolution on a big or high-resolution panel,
  4x or 8x MSAA is a legitimate visibility choice, not vanity.
- It is also the one common setting in CS2 that genuinely demands GPU performance. If
  you want 8x at high refresh, that is a hardware requirement, and it is the main
  scenario where a stronger graphics card is actually the correct purchase for this
  game.
- Only drop it if you are frame-starved and have already confirmed the GPU is your
  limit (see the 30-second check below).

Shadow quality is the other setting worth naming, but MSAA dominates.

### Where the ceiling changes hands

Now the question you actually want answered: **if the CPU and RAM are maxed out, at
what point does the GPU become the limit?**

Assume the CPU side is genuinely all in, meaning a 9800X3D class part with DDR5-6000
CL30 running 1:1, so the CPU is capable of roughly 500 to 700 FPS at competitive
settings.

Rather than list every card, group them into three classes. For CS2 this is enough
resolution, because the differences *inside* a class are far smaller than the difference
between being CPU-bound and GPU-bound:

| Class | RTX 3000 | RTX 4000 | RTX 5000 |
|---|---|---|---|
| **A: entry / mid** | 3050, 3060, 3060 Ti | 4060, 4060 Ti | 5060, 5060 Ti |
| **B: upper-mid / high** | 3070, 3070 Ti, 3080 | 4070, 4070 Super, 4070 Ti | 5070, 5070 Ti |
| **C: enthusiast / halo** | 3080 Ti, 3090, 3090 Ti | 4080, 4080 Super, 4090 | 5080, 5090 |

Class boundaries are soft and this is a CS2-usable grouping, not a general GPU
hierarchy. Generation-over-generation, the RTX 5000 parts land roughly 20% above their
4000-series counterparts, and the overlap is real: an RTX 4070 Ti actually measures
about 10% *faster* than an RTX 5070
([TechRadar](https://www.techradar.com/computing/gpu/i-cant-say-im-surprised-but-nvidias-rtx-5070-leaked-benchmark-reveals-up-to-20-percent-performance-boost-over-rtx-4070),
[technical.city](https://technical.city/en/video/GeForce-RTX-4070-Ti-vs-GeForce-RTX-5070)).
Which is precisely why a class is the right granularity here.

Against a maxed-out CPU, the classes land like this:

| Resolution + AA | Class A | Class B | Class C |
|---|---|---|---|
| 1280x960 or 1080p, no MSAA | CPU limits | CPU limits | CPU limits, by a wide margin |
| 1080p, MSAA 4x | GPU starts to limit | CPU limits | CPU limits |
| 1080p, MSAA 8x | GPU limits | GPU limits at the top end | CPU mostly limits |
| 1440p, no MSAA | GPU starts to limit | CPU limits | CPU limits |
| 1440p, MSAA 4x | GPU limits | GPU starts to limit | CPU mostly limits |
| 4K, any AA | GPU limits | GPU limits | GPU limits |

**The practical consequence for older cards:** an RTX 3070 or 3080 sits in the same
class as a 5070 for this purpose, and at competitive settings it is not your limit. CS2
also barely touches VRAM (see below), and NVIDIA Reflex support goes back well before
the 3000 series, so neither is a reason to replace a working card. If you are on a 3000
or 4000 series part and CS2 feels slow at 1280x960, the answer is upstream in the CPU and
memory, not in the graphics card.

Read this as orientation, not as measured law. It is an inference from the mechanics
plus the sources above, and CS2-specific GPU data with rigorous methodology barely
exists (see the note on benchmark quality in the CPU section). The pattern it
encodes is well supported, though:

- **At competitive settings, essentially nothing limits before the CPU.** A Class A card
  is enough to keep up with a maxed-out CPU at 1280x960 with AA off. This is why
  buying a bigger GPU for CS2 is the classic wasted upgrade.
- **MSAA is what buys the GPU a seat at the table.** If you want 4x or 8x at high
  refresh, that is a real GPU requirement and the only common one in this game.
- **4K is the only resolution where the GPU is unambiguously the wall**, at every tier.
- Between those extremes, the two are close enough that the answer depends on your map,
  your settings and your specific silicon, which is why you should measure rather than
  guess.

### How to tell which one is limiting you, in 30 seconds

Do not infer this from a table, including the one above. Open any overlay that shows
GPU load (HWInfo64, the NVIDIA overlay, CapFrameX sensors) and play a round:

| Symptom | Verdict |
|---|---|
| GPU load 95%+ | GPU-bound. A better card, lower resolution or less MSAA will help. |
| GPU load 50 to 80%, one CPU thread near 100% | CPU-bound. A better GPU changes nothing. |
| GPU load low **and** no CPU thread saturated (75 to 90%) | Memory-latency bound. See [exp-004](../experiments/exp-004-fps-ceiling-memory-bound.md); check your 1:1 ratio and consider an X3D part. |

That third row is the one people misread as "my PC is fine, the game is badly
optimized". It is a real, diagnosable state, and it is the reason the RAM ratio section
above exists.

### VRAM

VRAM amount does not affect CS2 FPS or stability at any competitive resolution.
CS2 uses minimal VRAM. 6GB is sufficient at 1440p. 8GB is comfortable headroom.
Choosing a GPU for more VRAM to help CS2 is not a valid reason.

### When to upgrade your GPU for CS2

Only if one of these is true:
- Your GPU cannot maintain your target FPS even after CPU/RAM are optimized, verified
  with the 30-second check above rather than assumed
- You play at 1440p or higher and want consistent 300+ FPS
- **You want 4x or 8x MSAA at high refresh.** This is the most common legitimate reason
  in practice. 8x still costs roughly 12% even on a fast rig and close to 30% on a
  slower one, and plenty of players including donk run 8x deliberately for image
  clarity at stretched resolutions

Otherwise, GPU money is better spent on CPU, RAM, or monitor for CS2 specifically.

### VRR FPS cap: how to compute it

For G-Sync Compatible / FreeSync displays, your FPS cap needs to sit just
below the panel's max refresh so the VRR window stays active and V-Sync
never has to engage. The rule the community used in the 60–240Hz era was
"refresh minus 3" (so 237 for 240Hz, 357 for 360Hz). That fixed-FPS
heuristic does not scale cleanly to 360/480/540Hz panels. Driver-side LFC
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

## Monitor: the most important hardware decision for CS2

The monitor is where all the hardware work becomes visible. A CPU that produces
500 FPS delivers nothing extra to a 144Hz monitor. The monitor is the final
bottleneck in the chain, and for most players, it's the most under-invested component.

### Refresh rate: the primary spec

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

### Panel types: what actually matters for CS2

This is where most monitor guides mislead. The answer for CS2 is not the same
as the answer for a photo editor.

**TN (Twisted Nematic)**
- Response time: 1–2ms GtG, fastest pixel switching of any panel type
- Color: worst, narrow viewing angles, washed-out colors
- Why pros use it: raw speed, and ZOWIE's dominance (see below)
- Verdict: technically optimal for pure response time, but color difference vs modern IPS is meaningful

**IPS (In-Plane Switching)**
- Response time: 1–4ms GtG on modern fast-IPS panels
- Color: excellent, wide angles, accurate representation
- Verdict: best balance for competitive CS2. Modern fast-IPS at 360Hz is within
  1–2ms of TN while providing significantly better image quality

**VA (Vertical Alignment)**
- Response time: 4–6ms GtG, and dark-scene pixel transitions are noticeably slower
- The problem: dark areas of CS2 maps (smoke, shadow zones, tunnels) show smearing
  as pixels transition slowly. VA's slow dark-to-dark pixel transitions produce
  measurably longer motion blur than IPS, dark transitions being significantly slower
  than bright-to-bright, a pattern confirmed across multiple display reviewers
  (Monitors Unboxed, RTINGS).
- Verdict: **avoid for CS2.** The dark-scene smearing is a direct gameplay disadvantage.

**OLED**
- Response time: ~0.02–0.03ms GtG, effectively instant pixel switching
- Color: best available, true blacks, perfect contrast
- Refresh rate ceiling (2026): 480Hz QD-OLED ([LG 27GX790A](https://www.tomshardware.com/monitors/gaming-monitors/asus-world-first-oled-esports-monitor-can-hit-540hz-at-1080p-rog-strix-oled-model-among-four-fresh-offerings)),
  540Hz Tandem WOLED ([ASUS XG259QWPG Ace](https://rog.asus.com/articles/gaming-monitors/the-rog-strix-oled-xg259qwpg-ace-combines-oled-performance-and-tournament-grade-specs/),
  June 2026), 540Hz QD-OLED 1440p ([ASUS PG27UCDM](https://www.tomshardware.com/monitors/gaming-monitors/asus-world-first-oled-esports-monitor-can-hit-540hz-at-1080p-rog-strix-oled-model-among-four-fresh-offerings)),
  and 540Hz at 1440p with a 720Hz dual-mode at 1280x720 on Primary RGB Tandem WOLED
  ([ASUS PG27AQWP-W](https://tftcentral.co.uk/reviews/asus-rog-swift-pg27aqwp-w)).
  The 240Hz OLED ceiling no longer applies, and OLED now matches or exceeds Fast-TN on
  refresh rate.
- Burn-in risk: static HUD elements (radar, HP bar, crosshair) can cause burn-in with
  extreme prolonged use. Modern gaming OLEDs include pixel-shift and compensation cycles
  that reduce but don't eliminate this risk. Documented cases exist. Players putting
  4+ hours daily into CS2 at high brightness long-term should factor this in.
- VRR Flicker caveat: OLED gamma is calibrated for fixed refresh; under VRR the
  refresh varies and the calibrated curve drifts, producing visible brightness/gamma
  shift especially in dark UI areas. See
  [exp-001 § Conclusion](../experiments/exp-001-vrr-vs-fixed-refresh.md).
- Verdict: exceptional display quality with refresh rates now matching TN at 540Hz
  and approaching 600Hz Fast-TN. The case for TN over OLED in 2026 is essentially
  tournament-supply parity, not technical superiority.

### GtG vs MPRT: which response time matters

Two response time metrics appear on monitor specs. They measure different things:

**GtG (Grey-to-Grey):** How long a pixel takes to switch between two grey shades.
This is the physically meaningful metric, it directly measures how fast the display
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
Hz. The variance between models at the same Hz is real but often within measurement
noise (~±2ms in standardized testing).

For scale, the ASUS ROG Swift PG27AQWP-W (27", 1440p, 540Hz native, 4th-gen Primary RGB
Tandem WOLED) measured **2.29ms total input lag** (1.17ms minimum, 3.12ms maximum) with
a 0.73ms average response time, which reviewers described as the lowest or joint-lowest
input lag they had ever recorded
([Tom's Hardware](https://www.tomshardware.com/monitors/gaming-monitors/asus-rog-swift-pg27aqwp-w-27-inch-540-hz-qhd-qd-oled-gaming-monitor-review),
[TFTCentral](https://tftcentral.co.uk/reviews/asus-rog-swift-pg27aqwp-w)). At 540Hz a
single frame is 1.85ms, so that display's entire signal-to-photon delay is on the order
of one frame.

#### The "always enable Game Mode" advice needs a caveat

The classic warning is that picture modes like "cinema" or "vivid" add 10 to 20ms of
processing and that Game Mode bypasses it. That is true, and important, on **televisions
and general-purpose displays**, which run motion interpolation, noise reduction and
scaling pipelines that genuinely cost that much.

It does not transfer cleanly to a dedicated gaming monitor. On a panel already measuring
around 2ms end to end, there is no room for a 10 to 20ms processing stage hiding in a
colour preset: those presets change gamma, saturation and colour space, not the signal
path. So if Game Mode makes your colours look bad, which is a common complaint on OLEDs
because the presets tend to push saturation and skew gamma, **you do not have to keep it
on for latency reasons.** Pick the mode that looks right.

What actually costs latency, and what to watch instead:

- **Motion blur reduction / backlight or panel strobing** (ELMB and similar). This adds
  latency and usually disables VRR. It is a real trade, unlike a colour preset.
- **Any scaling.** Feeding a non-native resolution and letting the monitor stretch it
  can add a processing step. Prefer GPU scaling, and see
  [Fundamentals](../fundamentals/README.md) for the stretched-resolution discussion.
- **VRR plus an incorrect FPS cap**, which pushes you into V-Sync. See
  [VRR FPS cap](#vrr-fps-cap-how-to-compute-it) above.

For colour on a wide-gamut OLED specifically, the usual fix for oversaturation is an
sRGB or gamut-clamped mode rather than the default gaming preset, plus the display's
uniform-brightness option if you dislike ABL brightness shifts.

**Honest limitation:** the reviews above measure input lag for the display, not
per picture mode. Nobody publishes per-preset lag numbers for these monitors, so the
claim here is an inference from the total measured lag and from what those presets do,
not a direct per-mode measurement. If you want certainty on your own panel, compare
modes with a latency tool rather than trusting either version of the advice.

### What pros actually use, and why

As of 2026, ZOWIE dominates the professional CS2 scene:

- ZOWIE is the official monitor partner for IEM and ESL events through 2027.
- ~90% of tracked CS2 pros use ZOWIE monitors, predominantly XL2566K (360Hz TN),
  XL2586X (540Hz Fast-TN with DyAc 2), and the [XL2586X+ at 600Hz](https://forums.guru3d.com/threads/zowie-presents-its-xl2586x-esports-monitor-with-a-staggering-600hz-refresh-rate.454896/)
  on the IEM/ESL stage rotation.
- The panel is TN, not because TN is technically superior to modern fast-IPS or
  Tandem WOLED at the same refresh, but because **ZOWIE is the tournament standard
  and pros train on what they play on**.

This is an important nuance: pro monitor choice is driven by tournament
standardization and supply, not by a unanimous technical verdict. Modern
fast-IPS at 360Hz and 540Hz Tandem WOLED ([ASUS XG259QWPG Ace](https://rog.asus.com/articles/gaming-monitors/the-rog-strix-oled-xg259qwpg-ace-combines-oled-performance-and-tournament-grade-specs/))
are technically competitive with TN for response time while providing better
image quality. The pro consensus on ZOWIE TN reflects habit, ecosystem, and
tournament supply, not a measured technical preference.

---

## The upgrade path: priority order

If you're upgrading specifically for CS2 performance, this is the order that
produces the most improvement per euro spent:

| Priority | Upgrade | Why |
|---|---|---|
| 1 | **Monitor Hz** (to 240Hz minimum) | Removes the display ceiling, all other hardware improvements are invisible below your Hz |
| 2 | **CPU** (to X3D or high-clock equivalent) | The actual bottleneck, biggest FPS gains here |
| 3 | **RAM** (DDR5-6000 CL30 dual channel for AM5, verified running 1:1) | Feeds the CPU, significant for X3D especially |
| 4 | **Monitor Hz** (240 → 360Hz) | Only meaningful after CPU/RAM are not bottlenecking |
| 5 | **GPU** | Only if CPU/RAM are optimized and GPU is still the ceiling |
| never | **Storage** | Zero effect on frame rate. Buy capacity, not speed |

**The most common mistake:** Buying a better GPU before addressing CPU and RAM.
The GPU does nothing for CS2 FPS when the CPU is already the bottleneck.

**The most expensive mistake per euro:** Buying a high-MT/s memory kit that cannot run
1:1. You pay more for a number on the box and get worse latency than the cheaper kit,
in a game that is bound by latency.

**The second most common mistake:** Upgrading to 360Hz before the CPU can produce
360+ FPS consistently. A 360Hz monitor displaying 180 FPS gives no advantage over
a 240Hz monitor displaying the same 180 FPS.

Hardware and software must scale together. The weakest link determines the result.

---

← [Back to compendium](../README.md) → [Fundamentals](../fundamentals/README.md)
