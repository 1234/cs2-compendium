# Mouse Polling Rate

Most guides tell you higher is better. A few tell you it costs CPU.
Neither explains what's actually happening — or when it matters for CS2 specifically.

This section does.

---

## What polling rate actually is

The mouse doesn't push data to your PC continuously. Your PC asks for it.

At a fixed interval, the USB controller queries the mouse: *"Where are you? What changed?"*
The mouse sends back its accumulated motion data. That cycle is the polling rate.

| Polling rate | Interval | Reports per second |
|---|---|---|
| 125 Hz | 8 ms | 125 |
| 500 Hz | 2 ms | 500 |
| 1000 Hz | 1 ms | 1,000 |
| 2000 Hz | 0.5 ms | 2,000 |
| 4000 Hz | 0.25 ms | 4,000 |
| 8000 Hz | 0.125 ms | 8,000 |

Each report triggers a USB interrupt — a signal to the CPU that says *"stop what you're doing,
handle this."* The CPU's interrupt handler processes the data, routes it through the OS HID stack,
and places it in a buffer the game reads from.

At 1000 Hz, that's 1,000 interrupts per second. At 8000 Hz, it's 8,000.

---

## The latency math

The polling interval is the *maximum* delay between physical movement and the PC knowing about it.
On average, the delay is half that — movement doesn't align to poll boundaries.

| Polling rate | Max latency | Average latency |
|---|---|---|
| 1000 Hz | 1 ms | ~0.5 ms |
| 4000 Hz | 0.25 ms | ~0.125 ms |
| 8000 Hz | 0.125 ms | ~0.0625 ms |

The jump from 125 Hz to 1000 Hz saves up to 7 ms of average latency. That's real and perceptible.

The jump from 1000 Hz to 4000 Hz saves ~0.375 ms on average.
The jump from 4000 Hz to 8000 Hz saves ~0.0625 ms on average.

Human perception threshold for input latency: roughly 1–2 ms. Both of these jumps fall below it.

---

## The CPU cost — and why it matters specifically in CS2

Every interrupt takes time. Not much individually — but at 8000 Hz, the aggregate is significant,
and where the cost lands is the real problem.

### The numbers

| Polling rate | Approx. CPU overhead |
|---|---|
| 1000 Hz | ~0.1% — negligible |
| 4000 Hz | ~2–3% of one core |
| 8000 Hz | ~5–7% of one core |

Those percentages look small. But they're not spread across all cores. They land on
**one core** — the core handling USB interrupts.

On a 6-core CPU, 5–7% of one core can represent 30–45% of that core's capacity.
If that same core is also running the game engine's main thread, it's now competing
with itself.

### Why this is a CS2-specific problem

CS2 on Source 2 is heavily CPU-bound. It uses all available cores, but the game thread —
the one that runs the simulation, processes inputs, and determines what gets rendered —
is still largely single-threaded in its hottest paths. It maxes out one core in ways
other engines don't.

Now add 8000 Hz polling. The USB interrupt handler and the game simulation thread
are both fighting for the same core. The result isn't a clean 5% FPS loss.
It's frame time spikes — frames that take longer than they should at irregular intervals.
Those spikes don't show up in average FPS. They show up in 1% lows and perceived stutter.

Real-world measured impact on CS2 (mid-range CPU, Ryzen 5000 series):
- 1000 Hz → baseline
- 4000 Hz → ~10–20% FPS reduction on affected cores; visible in 1% lows
- 8000 Hz → ~20–33% FPS reduction; consistent frame time variance

A user reports going from 1200 to 800 FPS switching from 1000 Hz to 8000 Hz on a Ryzen 7 5700X.
That's not a rounding error.

### DPC latency

Each interrupt also generates a DPC — a Deferred Procedure Call. The OS schedules these
after the interrupt itself, to handle the actual data routing.

At 1000 Hz, DPC latency stays under 100 μs. At 8000 Hz, measured spikes reach 500–2000 μs.
These DPC spikes are the mechanism behind the frame time variance.
Tools like LatencyMon will show this clearly on any affected system.

---

## How frame rate interacts with polling rate

This is the part most guides skip entirely.

The game reads your mouse position when it renders a frame. Not between frames.
If the game is rendering 300 frames per second, it reads mouse position 300 times per second —
regardless of how many times the mouse reported in between.

Extra reports between frames aren't wasted entirely — they allow the game to use the
most recent position at the moment of frame render. But the benefit has a ceiling.

```
Useful polling reports per frame = polling rate ÷ FPS
```

| FPS | 1000 Hz | 4000 Hz | 8000 Hz |
|---|---|---|---|
| 60 | 16.7 reports/frame | 66.7 | 133.3 |
| 144 | 6.9 reports/frame | 27.8 | 55.6 |
| 240 | 4.2 reports/frame | 16.7 | 33.3 |
| 300 | 3.3 reports/frame | 13.3 | 26.7 |

At 300 FPS, 1000 Hz already delivers 3–4 reports per frame. The game has a fresh,
precise position on every frame. Going to 4000 Hz gives 13 reports per frame —
the position read at render time is marginally more current.

But going from 4000 Hz to 8000 Hz at 300 FPS? The frame rate is now the hard bottleneck.
The extra reports exist, but the game only reads position once per frame regardless.
The CPU cost is real. The benefit is sub-frame-time — and the frame is your unit of perception.

**The practical ceiling:**
- Below 240 FPS: 1000 Hz is already sufficient
- At 240–300 FPS: 4000 Hz offers marginal but real improvement in position precision
- Above 300 FPS on 360 Hz+: 4000 Hz captures ~75% of all theoretical polling latency gains vs. 8000 Hz, at 1/8th the CPU cost

---

## Does higher polling rate actually improve aim?

Theoretically yes. In practice, the numbers don't support the claim beyond 4000 Hz.

### Cursor tracking precision

At 1000 Hz with a 240 Hz monitor, position jitter between frame reads is roughly 0.3 ms.
At 8000 Hz with a 360 Hz monitor, it drops to ~0.04 ms.

That improvement is real. It is also below human perception threshold under any testing condition.

### Blind tests

Controlled blind tests — where players can't see what polling rate they're using —
show the following:

- 125 Hz vs. 1000 Hz: Players can reliably distinguish these. The improvement is real and felt.
- 1000 Hz vs. 4000 Hz: Most players cannot reliably distinguish these in blind conditions.
- 4000 Hz vs. 8000 Hz: No tested population has shown statistically reliable discrimination.

### What the margin actually represents

The improvement from 4000 Hz to 8000 Hz is 0.0625 ms of average latency.

Human reaction time: ~150–250 ms.
The improvement is 0.025–0.04% of that.

Network latency to a typical CS2 server: 10–50 ms.
Server tick: ~64 Hz (15.6 ms per tick despite sub-tick input processing).

The 0.0625 ms improvement from 8000 Hz is being added to a system that already
has 15+ ms of server-side latency. It doesn't disappear — it's just structurally irrelevant.

### Pro usage

As of 2025, the overwhelming majority of CS2 professionals use 1000 Hz.
A small number — estimated under 5% — use 4000 Hz.
8000 Hz is essentially absent in professional CS2, not because pros are uninformed,
but because the stability risks aren't worth the unmeasurable gain.

---

## CS2-specific issues with high polling rates

Source 2 has a documented problem with high polling rates that makes this more than an
abstract CPU concern.

### SDL input handler lock contention

CS2 uses SDL's `GetRawInputData` to process mouse input on Windows.
The SDL implementation acquires a lock on every call. At 1000 Hz, this is fine —
1000 lock acquisitions per second is trivial. At 8000 Hz, 8000 lock acquisitions
per second creates measurable contention with the game thread.

This isn't speculation. It was identified in SDL's GitHub issue tracker (#8756)
and causes input lag that's independent of, and in addition to, the DPC latency overhead.
The result: 8000 Hz can produce worse effective input timing in CS2 than 1000 Hz
even on systems with no CPU constraint at all.

### Inconsistent advertised polling rates

The Razer Viper 8KHz, the product that put 8000 Hz on the map, routinely achieves
3600–5000 Hz in practice on Windows — not 8000 Hz. Driver overhead, USB scheduling,
and Windows' interrupt coalescing all reduce the effective rate.

8000 Hz on the box doesn't mean 8000 Hz in the game.

### Post-update erratic behavior

CS2 updates in 2024 caused documented cases of erratic mouse behavior — pointer racing,
inconsistent sensitivity — specifically at 8000 Hz. Rolling back to 1000 Hz resolved
the issue consistently. The root cause was never officially acknowledged by Valve.

---

## Hardware debounce and polling rate

Every click generates mechanical switch bounce — the switch briefly makes and breaks
contact multiple times in milliseconds before settling. Hardware debounce is a firmware-level
delay that waits out the bounce before registering the click.

Typical debounce times: 4–8 ms for mechanical switches.

### How polling rate changes the risk profile

At 8000 Hz, the mouse is checking button state every 0.125 ms. A 1 ms bounce window
contains 8 potential reads. Low debounce times that are safe at 1000 Hz can produce
double-click registrations at 8000 Hz — because the mouse catches more of the bounce cycle.

| Setup | Risk |
|---|---|
| Mechanical switch + 1000 Hz + 6 ms debounce | Safe, standard |
| Mechanical switch + 4000 Hz + 4 ms debounce | Acceptable with quality switches |
| Mechanical switch + 8000 Hz + 2 ms debounce | Double-click risk depending on switch quality |
| Optical switch + any polling rate + 0 ms debounce | Safe — no mechanical bounce to catch |

Optical switches (Razer Optical, Corsair Champion) have no mechanical bounce.
They can run 0 ms debounce at any polling rate. They eliminate this interaction entirely.

Mice marketed specifically as "8000 Hz ready" typically either use optical switches
or have been tuned with this interaction in mind. Generic budget mice with high polling
rates often haven't.

---

## When higher polling rate actually helps

Be precise about the conditions. All three need to be true simultaneously.

**1. Monitor refresh rate ≥ 240 Hz**
Below this, the monitor is your bottleneck. Polling rate improvements are absorbed
by the display and never reach your perception.

**2. Consistent in-game FPS ≥ 240**
If FPS drops below your polling rate's useful ceiling during play, you've traded
CPU headroom for nothing.

**3. Modern CPU with spare single-core capacity**
Ryzen 7 7700X and up, Intel 13th gen i7 and up. On anything older or lower,
the CPU cost of 4000 Hz+ directly competes with the game thread.

If all three apply: **4000 Hz is a defensible upgrade.** It captures ~75% of the
theoretical latency improvement from 1000 Hz to 8000 Hz at 1/8th the CPU cost,
and avoids the CS2-specific compatibility problems.

**8000 Hz is not defensible for CS2 under any common configuration** due to the SDL
lock contention issue, driver instability, and the negligible latency delta over 4000 Hz.

---

## The marketing reality gap

Manufacturers quote theoretical best-case specs. Those specs don't survive contact with
actual operating conditions.

| Marketing claim | Reality |
|---|---|
| "0.125 ms latency at 8000 Hz" | Average is 0.0625 ms; human threshold is ~1–2 ms |
| "Noticeable improvement over 1000 Hz" | Blind tests: 85–90% of players can't distinguish 4K from 8K |
| "Improved aim and spray control" | No measurement methodology has shown statistically significant improvement |
| "Runs at 8000 Hz" | Razer Viper 8KHz achieves 3600–5000 Hz in practice on Windows |
| "Minimal CPU impact" | 5–7% total, concentrated on one core — 30–50% single-core impact |

The 8000 Hz race among manufacturers follows the same pattern as the DPI race before it.
DPI went from 400 to 800 to 1600 to 25,600. Playable range: 400–3200.
Polling rate is following the same arc. The numbers go up. The playable benefit doesn't.

---

## The verdict

```
240 Hz monitor + consistent 240+ FPS + modern CPU?
  → 4000 Hz. Captures most of the theoretical gain, none of the CS2-specific problems.

Anything else?
  → 1000 Hz. It is the professional standard, the stable choice, and
    the right answer for the overwhelming majority of setups.

8000 Hz in CS2?
  → No. SDL lock contention alone disqualifies it until Valve addresses the input handler.
```

Higher polling rate does not fix crosshair placement.
It does not fix spray control.
It does not compensate for 30 ms of server latency.

At 1000 Hz, polling is already the smallest source of latency in your system.
The rest of the compendium covers the parts that actually move the needle.

---

← [Back to Peripherals](README.md) | ← [Back to compendium](../README.md)
