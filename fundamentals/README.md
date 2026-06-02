# Fundamentals

The non-obvious foundations that affect everything else.

Most players adjust settings without understanding what they're actually changing.
When something feels wrong, they don't know where to look — because they never
understood how the pieces connect in the first place.

This section explains the underlying mechanics. Not "set this to that" — but what
actually happens when you do.

---

## FPS and Input Latency

FPS is not just a smoothness metric. It directly determines how quickly your inputs
reach the screen.

### The math

Every frame takes time to render. That time is:

```
Frame time = 1000 ÷ FPS  (in milliseconds)
```

| FPS | Max frame time |
|---|---|
| 60 | 16.67 ms |
| 144 | 6.94 ms |
| 240 | 4.17 ms |
| 300 | 3.33 ms |
| 540 | 1.85 ms |

This frame time is the maximum delay introduced by the rendering step alone —
from input being captured to it appearing in a completed frame. It is one component
of total input latency, alongside display processing (~1–5ms on modern monitors)
and input polling rate (~1ms at 1000Hz). But it's the component most directly
controlled by your FPS, and often the largest one.

At 60 FPS that wait can be nearly 17ms. At 540 FPS it's under 2ms.

### Why FPS above your monitor's refresh rate still matters

Your monitor at 144Hz displays a new image every 6.94ms. If your GPU is rendering
500 FPS, most of those frames are never shown. This seems wasteful — and by one
measure, it is. But it still matters:

With more frames being produced, the frame that arrives at your monitor at the moment
it refreshes is statistically more recent. The input that was baked into it happened
more recently. The effective latency between your physical input and what you see
is lower — even if your monitor is only updating 144 times per second.

This is why uncapped FPS with a high Hz monitor is better than capping at the monitor's
refresh rate. The frames you don't see still reduce the age of the ones you do.

### The render queue problem — and what Reflex does

Even with high FPS, there's a second latency source: the CPU-GPU pipeline.

The CPU prepares frames and queues them for the GPU to render. If the CPU works
faster than the GPU, frames stack up in this queue. Your input is in a frame that's
waiting behind other frames. By the time it reaches the screen, it's already stale.

NVIDIA Reflex paces CPU work so it completes exactly when the GPU is ready —
eliminating the queue. No frames waiting, no stale inputs.
This is why Reflex is one of the highest-impact settings in CS2 — it addresses a
latency source that higher FPS alone does not fix.

---

## Monitor Refresh Rate

Your monitor's refresh rate is the ceiling for how often a new frame can be displayed.
Higher refresh rate = more opportunities per second to show a fresh frame = lower
display latency.

| Refresh rate | Interval between frames |
|---|---|
| 144 Hz | 6.94 ms |
| 240 Hz | 4.17 ms |
| 360 Hz | 2.78 ms |
| 540 Hz | 1.85 ms |

### Diminishing returns — where they actually are

The perceptual benefit of higher Hz follows a curve, not a line:

| Jump | Latency saved |
|---|---|
| 60 → 144 Hz | 9.73 ms |
| 144 → 240 Hz | 2.77 ms |
| 240 → 360 Hz | 1.39 ms |
| 360 → 540 Hz | 0.93 ms |

The 60 → 144Hz jump is massive and perceptible to almost everyone.
The 144 → 240Hz jump is smaller but still meaningful in competitive play.
Above 240Hz, the differences become increasingly difficult to perceive for most players —
though they remain measurably real at the hardware level.

**The competitive floor is 240Hz.** 144Hz is viable for ranked play but is now
considered outdated at the serious competitive level — the hardware has become
accessible enough that 240Hz is the minimum expectation.
360Hz is the current professional standard. The jump from 240 to 360 saves 1.39ms
per frame — small in isolation, but at pro level reaction times, meaningful.

### Screen tearing

When FPS exceeds the monitor's refresh rate and VSync is off, you get tearing —
the monitor displays part of one frame and part of the next simultaneously.

Tearing happens because the GPU delivers a new frame while the monitor is mid-refresh.
The top half of the screen shows frame N+1 while the bottom still shows frame N.

VSync eliminates tearing by holding frames until the next refresh cycle. The cost:
up to one full refresh interval of added latency (6.94ms at 144Hz). For competitive
play, that cost is too high. Turn VSync off and accept the tearing.

NVIDIA Reflex with G-Sync can eliminate tearing without the VSync latency penalty —
but this requires a G-Sync compatible monitor. Without G-Sync, Reflex alone does not
eliminate tearing. The practical recommendation for most setups: VSync off, Reflex on,
accept occasional tearing as the tradeoff for minimum latency.

---

## Resolution and Field of View

Resolution determines how many pixels CS2 renders. Field of view determines how much
of the game world fits on your screen.

### CS2's FOV system

CS2 uses a fixed horizontal FOV of 90° at 4:3 aspect ratio.
At 16:9, the horizontal FOV expands to approximately **106°**.

| Aspect ratio | Horizontal FOV |
|---|---|
| 4:3 | ~90° |
| 16:9 | ~106° |

This means 16:9 players see more of the world horizontally than 4:3 players.
A CT holding an angle on the far left of the screen is simply not visible to a
4:3 player who would see them on 16:9.

### 4:3 Stretched vs 4:3 Black Bars

Both use the same 90° horizontal FOV. The gameplay field of view is identical.
The difference is purely visual:

- **Black bars:** The 4:3 image is displayed at its natural proportions with empty
  black columns on left and right. Objects and players appear at their correct width.
- **Stretched:** The 4:3 image is stretched horizontally to fill the full 16:9 screen.
  Player models appear **1.33× wider** than they would at native resolution.

The wider player models in stretched mode make enemies an easier target to track and
hit — the visual target is 1.33× wider. Critically: **hitboxes do not scale**.
The actual collision geometry stays the same size as at native resolution. What
changes is how much screen space the visual model occupies, making it easier to
keep your crosshair on target. According to prosettings.net, roughly 59% of CS2 pros
use 4:3 stretched specifically, and around 75% play on 4:3 in any form (stretched or
black bars).

The tradeoff: reduced FOV (you see less horizontally), and the visual distortion
takes time to adapt to if you're coming from native resolution.

### Rendering resolution and performance

A lower resolution renders fewer pixels — at 1280×960 (4:3) the GPU renders
~1.23M pixels per frame. At 1920×1080 (16:9) it renders ~2.07M — roughly 69% more.

Since CS2 is CPU-bottlenecked, the GPU usually has headroom regardless of resolution.
In practice, for most modern setups the FPS difference between 4:3 and 16:9 is smaller
than commonly assumed — the CPU ceiling typically limits both before the GPU becomes the
constraint. Test both with your actual hardware.

---

## Raw Input

### What the Windows input stack does

When your mouse moves, the signal travels through the Windows kernel HID stack —
`HIDClass.sys` → `mouhid.sys` → `mouclass.sys` — before any user-space application
sees it. From there it can take two paths:

- **Legacy path (`WM_MOUSEMOVE`):** the kernel hands the movement to the Windows
  cursor/pointer pipeline, which applies the pointer-speed multiplier (6/11 = 1:1)
  and, if enabled, *Enhance Pointer Precision* — a non-linear acceleration curve
  that makes fast flicks travel further than slow ones. The app receives a cursor
  delta that has already been mangled.
- **Raw Input path (`WM_INPUT`):** the application registers for the Raw Input API
  and receives the device-level delta straight from the HID stack, before the
  pointer pipeline touches it.

For desktop use, the legacy pipeline is intentional. For CS2, it would be catastrophic —
every muscle memory rep you build would be built against a moving target.

### What raw input actually bypasses

Raw Input does **not** skip the HID stack. The data still flows through
`HIDClass.sys` → `mouhid.sys` → `mouclass.sys` in the kernel. What it bypasses is
the **Windows pointer pipeline** — pointer speed, *Enhance Pointer Precision*, and
the legacy `WM_MOUSEMOVE` message that carries those modifications. The DPI set on
the mouse itself is the only multiplier left in the chain. Physical movement to
in-game camera movement is linear and consistent.

In CS2, raw input is forced on and cannot be disabled — Valve removed the option.
The `m_rawinput` cvar was removed with it. There is nothing to toggle in-game.

### Why you still disable EPP and fix pointer speed

A fair question follows: if CS2 forces raw input and raw input bypasses the
Windows pointer pipeline, why bother disabling *Enhance Pointer Precision* and
setting pointer speed to 6/11 at all? In-game CS2 aim, on its own, is genuinely
unaffected by either setting.

The reasons to still disable EPP and set pointer speed to 6/11:

- **Aim trainers do not all use Raw Input.** Aim Lab and some Kovaak's profiles
  read mouse input through the legacy `WM_MOUSEMOVE` path, so EPP and pointer
  speed mangle the curve during warmup. You then load into CS2 with raw deltas
  and your warmup reps were built against a different input curve than the one
  you play on.
- **Alt-tab and menu cursor feel.** Buy menu, scoreboard overlays, alt-tab to
  Discord or a browser between rounds — all of that runs on the legacy pointer
  pipeline. An EPP curve makes the cursor feel different on alt-tab than in-game,
  which is a constant low-grade distraction.
- **Vendor software acceleration is not bypassed.** As covered above, any
  acceleration configured in G HUB / Synapse / iCUE rides inside the HID stack
  and reaches CS2 untouched. EPP being off does not fix that — but leaving EPP
  on while also leaving vendor acceleration on stacks two acceleration curves.
- **Universal pro and coach default.** Every documented pro config disables EPP
  and runs pointer speed at 6/11. There is no measurable cost to matching the
  default and no upside to deviating.

Set pointer speed to the 6th notch (1:1) and uncheck *Enhance pointer precision*
in Windows. Do it once. Move on.

**The vendor-software caveat:** mouse manufacturer software (Logitech G HUB,
Razer Synapse, SteelSeries GG, Corsair iCUE, Glorious Core) installs a filter
driver that sits **inside the HID stack, before Raw Input reads from it**. Any
acceleration, smoothing, or angle-snapping configured in that vendor app is baked
into the deltas CS2 receives. Raw Input does not strip it out. You have to disable
it in each vendor app explicitly — turn off *Acceleration*, *Smoothing*, *Angle
Snapping*, and any *Pointer Precision* / *Enhanced Movement* equivalent. Setting
polling rate and DPI in the vendor app is fine; anything that modifies the movement
curve is not.

---

## The Network

### Ticks — how CS:GO worked

CS:GO's game server processed the world at fixed intervals called ticks.
At 64-tick (Valve matchmaking), the server updated 64 times per second — once every
15.625ms.

The problem: inputs could only be registered at tick boundaries. If you fired your
weapon 1ms after a tick, your shot wasn't processed until the next tick — 14.625ms
later. Depending on when your input arrived, you could lose up to a full tick of
registration time (15.625ms) through no fault of your own.

128-tick (Faceit) halved this: 7.8ms maximum registration delay.

### CS2's sub-tick system

CS2 records every input — mouse click, key press, mouse movement — with a precise
timestamp at the moment it occurs. The server processes that input at its exact
timestamp, not at the next tick boundary.

The tick-boundary penalty per system:

| System | Base rate | Max tick-boundary delay |
|---|---|---|
| CS:GO 64-tick (Valve) | 64 Hz | 15.625 ms |
| CS:GO 128-tick (Faceit) | 128 Hz | 7.8 ms |
| CS2 (Valve matchmaking) | 64 Hz + sub-tick | eliminated for inputs |
| CS2 (Faceit) | 64 Hz + sub-tick | eliminated for inputs |

**Why 64Hz base rate?** Valve explicitly chose not to raise the base tick rate to
128Hz for accessibility — lower-end hardware struggles with higher server update
frequencies. Sub-tick was designed as the alternative: keep 64Hz for server load,
but process inputs at sub-millisecond precision within each tick.

### What sub-tick does and does NOT fix

This is where it gets nuanced — and where the competitive community is genuinely divided.
The common shorthand that "movement is clamped to 64Hz" is wrong, but so is the
opposite claim that sub-tick makes everything 1000Hz. The truth sits between them.

**What sub-tick does fix for movement:** start, stop, and direction-change events
are timestamped and applied at sub-tick precision within the next 15.625ms tick.
Testing by Hyperus102 measured roughly 0.15–0.3 unit position variance on a
counter-strafe in CS2, versus ~3.9 units in CS:GO 64-tick. The moment your key
goes down or up is honored at sub-tick precision, not snapped to the nearest tick
boundary. Counter-strafing and pixel-perfect stop-tap timings benefit from this.

**What still runs at 64Hz:** the physics step itself. Air acceleration, ground
friction sampling, animation networking, and position broadcasts to other players
all advance once per 15.625ms tick. The world state you see updating, and the world
state others see of you, are 64Hz streams with sub-tick event timestamps layered on top.

Sub-tick also does not affect:

- **Your ping** — network travel time is unaffected. Sub-tick processes inputs precisely
  when they arrive — but they still had to travel.
- **Peeker's advantage** — reduced but not eliminated. Network latency guarantees
  some advantage remains for the peeking player.
- **Packet loss** — dropped packets cannot be timestamped or processed.
- **Interpolation** — CS2 still interpolates remote player positions between updates.

**Practical implication:** do not cap FPS at 64 to "match the tick rate". You hear
this advice from people who think the server clamps everything to 64Hz boundaries.
It does not. The client must produce input timestamps at frame granularity for
sub-tick to have anything to work with. Capping FPS at 64 throws away the sub-tick
resolution sub-tick was designed to deliver. Run uncapped, or cap just below your
refresh ceiling — never at 64.

### The debate — is CS2 sub-tick better or worse than CS:GO 128-tick?

This is genuinely contested at the highest level. Both sides are represented by credible
voices:

**The case for sub-tick:** Hit registration is more precise. Shots fired at the exact
moment of movement register more reliably. The tick-boundary penalty is mathematically
eliminated for inputs.

**The case for 128-tick:** Movement, spray patterns, and animation states still process
at 64Hz. Pros including ropz and SPUNJ have publicly stated they prefer 128-tick and
can feel the difference in movement responsiveness. SPUNJ explicitly requested Valve
allow 128-tick + sub-tick servers. Robin Kool (coach) demonstrated blind identification
of the tick rate difference.

**The honest answer:** Sub-tick solves one problem (hit registration at tick boundaries)
while the 64Hz base rate creates a different one (movement/spray processing fidelity).
Whether the tradeoff is positive depends on what you're most sensitive to.
The debate is not resolved, and pretending there's consensus would be dishonest.

### What you can actually control on the network side

| Factor | Controllable? | How |
|---|---|---|
| Ping | Partially | Server region selection, wired vs wireless, ISP quality |
| Packet loss | Partially | Wired connection eliminates most local packet loss |
| Interpolation delay | Yes | `cl_interp 0`, `cl_interp_ratio 1` in autoexec |
| Peeker's advantage | No | Physics — can only be understood and played around |
| Sub-tick precision | No | Server-side, automatic |

The single most impactful network change for most players: **wired Ethernet over WiFi**.

WiFi introduces **jitter** — variable latency that fluctuates from packet to packet.
Your average ping might look acceptable (20ms), but if it swings between 15ms and 50ms
each packet, the game feels inconsistent in ways that average numbers don't show.

A stable 30ms wired connection plays better than a variable 15–50ms wireless one.
Jitter stability matters more than absolute ping value. Ethernet eliminates the
primary source of local jitter — a necessity for competitive play, not an optional upgrade.

---

← [Back to compendium](../README.md) → [Hardware](../hardware/README.md)
