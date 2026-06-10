# CS2 Compendium

You've watched the settings videos.  
You've copied the pro configs.  
You've read the Reddit threads where five people give five different answers.  
Something still isn't clicking — and nobody explains why.

That's the actual problem. Most CS2 resources give you the **what** without the **why**.  
Copy this. Set that. Good luck.

When it stops working — and eventually it does — you have nothing.  
No understanding. No way to adapt. Just another guide to google.

**This compendium goes the other way.**

Every setting has a reason. Every training decision has logic.  
Where pros and coaches genuinely disagree, we say so — both sides, no fake consensus.  
Nothing dumbed down. Nothing skipped. Nothing to sell you.

From first launch to the things most players never figure out in thousands of hours.

---

## The path that actually works

Most players skip the foundation and go straight to grinding.  
They build muscle memory on top of broken settings.  
Train habits on a platform that's working against them.  
Then wonder why nothing compounds.

There's an order. Here it is.

| Step | What | Why it comes first |
|---|---|---|
| **1. Windows** | Mouse, keyboard, power plan, HAGS, NVIDIA | If the OS is wrong, everything built on top is wrong |
| **2. Settings** | Video, launch options, autoexec.cfg | The platform CS2 runs on — fix it once, forget it |
| **3. Training** | KovaaK, Refrag, utility, KZ/bhop | The daily structure that compounds correctly on a correct foundation |
| **4+. Depth** | Mechanics, tactics, audio, callouts, hardware, demo review | What separates players who plateau from players who keep climbing |

Steps 1 and 2 take a few hours total. Then they're done — permanently.  
Everything after that builds on solid ground.

---

## What's here

### [Windows](windows/README.md)
The most underestimated section in this entire compendium.

Mouse acceleration on means every aim rep you do is training the wrong curve.  
Keyboard repeat delay too high means you're slower at everything — buys, strafes,
command inputs — without ever knowing it.  
Wrong power plan means inconsistent frame times no in-game setting can fix.

These aren't tweaks. They're prerequisites.

### [Settings](settings/README.md)
Every CS2 video option explained — not just what to set, but what it actually does
to your game when you change it. Launch options, NVIDIA program settings,
and a fully documented autoexec.cfg with reasons for every line.

### [Fundamentals](fundamentals/README.md)
The non-obvious things that affect everything else and that almost nobody explains:
resolution and aspect ratio, what FPS actually does at different levels, raw input,
network settings, and what's happening at the engine level when you change them.

### [Hardware](hardware/README.md)
CS2 is CPU- and RAM-limited. Not GPU-limited.  
Most players don't find this out until after they've spent money on the wrong thing.  
Covers what actually drives performance, what doesn't, and what to buy if you're
upgrading specifically for CS2. Includes a full monitor deep-dive.

### [Training](training/README.md)
A full 4-week monthly plan — KovaaK (HaiX playlists), Refrag, utility for all 7 maps,
KZ and bhop. Every decision in the schedule has a reason behind it.  
Grounded in real sources: direct pro quotes, aim coaching research,
and honest notes on where the evidence actually disagrees.

### [Peripherals](peripherals/README.md)
What actually affects click registration, polling rate, hardware debounce, the sub-tick
system — and a documented myth-busting section for the settings that get passed around
but have zero effect on CS2. Full peripheral deep-dives coming.

### [Measuring](measuring/README.md)
The tools (CapFrameX, HWInfo64, optional Claude Code MCP) and the controlled
A/B protocol used to validate recommendations in this compendium. Plus how
to read the numbers and distinguish CPU-bound from memory-bound bottlenecks.

### [Experiments](experiments/README.md)
Reproducible captures behind specific claims. Each experiment links to the
section it grounds — so you can read the recommendation, follow the link,
verify the methodology, and either confirm or contradict it on your own
hardware.

---

## What's coming

The foundation sections exist. These are next.

### Setup & Configuration

| Section | What it covers |
|---|---|
| **Crosshair** | What the parameters do, pro crosshair breakdowns, dynamic vs static |
| **Viewmodel** | FOV, X/Y/Z offset, bob — how much screen you lose and why it matters |
| **Sensitivity & eDPI** | DPI, in-game sens, eDPI, grip styles, how to actually lock your sens |
| **Binds & buy menu** | Grenade binds, jump-throw, fast buy, everything that isn't WASD but still costs rounds |
| **Monitor calibration** | Brightness, contrast, digital vibrance — this directly affects how well you see enemies |
| **Peripherals** | Sensor, shape, grip, wireless vs wired (2025), mousepads, polling rate — *stub exists, deep-dives coming* |
| **Mousepads** | Cloth vs hybrid vs glass, static friction vs glide, what changes with sweat/humidity, sizing |
| **Wireless vs wired mice** | CS2-specific latency, battery vs weight tradeoffs, why the 2024–25 pro rotation went wireless |
| **Keyboard switches & polling** | Linear vs tactile, actuation distance, polling rate ceiling, repeat delay revisited |
| **Audio setup** | HRTF, why to kill virtual surround, headset type, snd_ commands |
| **Faceit setup** | Anti-cheat install, client settings, why it feels different from Premier |

### Game Mechanics

| Section | What it covers |
|---|---|
| **Recoil & spray control** | Spray patterns per weapon, when to tap vs burst vs spray by range |
| **Weapon accuracy model** | First-shot accuracy, recovery times, movement penalties — the actual numbers |
| **Peeking mechanics** | Shoulder, jiggle, wide — peek advantage, defender advantage, when not to peek |
| **Movement mechanics** | Counter-strafing in depth, acceleration, crouch, shift walk |
| **Weapon deep-dives** | AWP quickscope, deagle one-tap, Glock vs USP, rifle vs pistol round meta |
| **Grenade mechanics** | CS2's volumetric smoke system, jumpthrow, one-way smokes, HE falloff |
| **Bomb mechanics** | Timer, kit timing, fake plant, post-plant audio, plant positions |
| **Sound as information** | Occlusion system, footstep distance, what carries through walls, bomb beep frequency |
| **Sound occlusion per material** | What carries through wood vs concrete vs metal — measured in-engine, not estimated |
| **Sub-tick — input timing experiments and demo analysis** | Empirical complement to the [Fundamentals § sub-tick](fundamentals/README.md#cs2s-sub-tick-system) explainer: counter-strafe timing under sub-tick, demo-side reconstruction, the 64Hz movement-step boundary, where sub-tick measurably helps vs where it's marketing |

### Tactics & Game Sense

| Section | What it covers |
|---|---|
| **Crosshair placement** | Highest ROI skill in CS2 — pre-aiming, height, angles, what separates Silver from Global |
| **Minimap reading** | What it tells you, how to read T movement, when to rotate |
| **Rotations & timing** | T-side timing, CT rotation windows, when to commit vs fake |
| **Default plays** | What a default is, why it matters, how it creates information |
| **Information game** | Trading info without dying, not over-committing, lurking fundamentals |
| **Retakes** | When to go, when to concede, utility usage |
| **Post-plant** | Passive vs aggressive, timing, how to play both sides |
| **Economy** | Full buy vs force vs eco, when to save, pistol round meta |

### Communication & Tools

| Section | What it covers |
|---|---|
| **Communication** | What info is worth saying, callout language, how pro comms differ from ranked |
| **Map callouts** | Complete reference for all 7 premier maps |
| **Demo review** | What to actually look for, POV vs GOTV, how often, how long |
| **Console commands** | Every command you'll actually reach for — fps, net, crosshair, practice server |
| **Workshop maps** | Prefire, retake, DM, aim maps — what exists, what's worth your time |
| **Tools & resources** | Leetify, csstats.gg, HLTV, prosettings.net, HWInfo64 — useful vs noise |
| **Ranking systems** | How Premier and Faceit ELO actually work, what separates each level mechanically |
| **Glossary** | eDPI, GtG vs MPRT, LFC, P1 / AdaptiveStd, sub-tick, jumpthrow, ANIMGRAPH 2, Reflex Boost, HAGS, VBS, Gear 1 vs 2, X3D — every acronym defined once |
| **CS2 launch flag changelog** | Patch-dated changes to launch options across CS2's lifetime — adapt to new patches yourself |

### Mental & Physical

| Section | What it covers |
|---|---|
| **Mental performance** | Tilt, session limits, variance vs mistakes, when to stop |
| **Physical health** | RSI prevention, posture, eye strain — this ends careers early |
| **Sleep & consolidation** | Muscle memory consolidates during sleep — why this is part of training, not separate from it |

### Reference & Validation

| Section | What it covers |
|---|---|
| **Wanted experiments** | A PR-able board of system + gameplay A/Bs the compendium wants but hasn't run yet |
| **Experiment template** | Copy-paste hypothesis / setup / method / results skeleton with naming convention reference |

---

## Contribute

See [CONTRIBUTING.md](CONTRIBUTING.md).

Something wrong? Open an **Issue**.  
Want to add something? Open a **Pull Request** — say what you're adding and why it belongs.  
Disagree with something? Open an Issue. This is only as good as the corrections it gets.

No affiliate links. No sponsored recommendations. Primary sources where possible.

---

## Credits

- Training routine by [HaiX](https://www.youtube.com/@HaiX)
- Pro quotes: esports.gg (donk), pley.gg (ZywOo), escorenews.com (NiKo, s1mple)
- Training research: Dignitas, Refrag, cs.money, ProSettings, 3D Aim Trainer, Turboboost
- Utility: Yprac, csnades.gg, cs2hype.com, Refrag guides
- KZ/movement: cybershoke.net, refrag.gg, xplay.gg, houseofclimb.com
- Maintained by **spnk**

---

## Tip jar

If any of this saved you a weekend of guessing, you can send a skin.
I love skins — graffiti, sticker, cheap case skin, anything counts.

Tradelink: <https://steamcommunity.com/tradeoffer/new/?partner=29765887&token=PXnLO_ha>

Real link only — if anyone DMs you claiming to be me with a different
link, it isn't.

The compendium stays the same either way. No tiers, no perks, no
priority issues — there's nothing to unlock.
