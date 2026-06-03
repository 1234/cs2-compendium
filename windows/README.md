# Windows

System-level settings that affect CS2 more than most in-game video options.  
These aren't tweaks. For most of them: set once, correct permanently.

The order below reflects priority. Start at the top.

---

## 1. Mouse — Enhanced Pointer Precision

**Where:** Settings → Bluetooth & devices → Mouse → Additional mouse settings → Pointer Options  
**Setting:** Uncheck *Enhance pointer precision*

This is Windows mouse acceleration. When it's on, moving the mouse fast covers more
distance than moving it slow — the cursor speed is non-linear.

In CS2 with raw input enabled (`m_rawinput 1`), Windows mouse settings are bypassed
for in-game movement. But Enhanced Pointer Precision still affects every other context —
the Windows desktop, the buy menu, the console, the scoreboard. And if raw input is
ever off, or breaks silently, you're training on an accelerated input without knowing it.

Turn it off system-wide. There is no argument for keeping it on.

**Pointer speed:** Set to 6/11 (the middle notch). At 6/11 with no acceleration,
Windows applies a 1:1 linear mapping with no multiplier. Any other setting adds a
multiplier on top of your DPI — even if acceleration is off.

**What does NOT affect your shooting:** Double-click speed has no effect on CS2 or any game
using raw input. → [Peripherals — Myths & what actually affects click registration](../peripherals/README.md)

---

## 2. Keyboard — Repeat Delay and Repeat Rate

**Where:** Control Panel → Keyboard → Speed tab  
*(Must use legacy Control Panel — Windows Settings does not expose this)*

| Setting | Value |
|---|---|
| Repeat delay | Short (all the way left) |
| Repeat rate | Fast (all the way right) |

This is the most overlooked setting in this entire section.

**What it does:** When you hold a key, Windows waits *Repeat Delay* milliseconds before
it starts repeating the input, then fires it repeatedly at *Repeat Rate*.

**Default values:** ~500ms delay, slow repeat rate.

**What this costs you in CS2:**

- **Buy menu:** Holding a buy key at default delay feels sluggish — you have to wait
  almost half a second before the input repeats. At fast rate, buys feel instant.
- **Console:** Navigating command history, scrolling, editing — all faster.
- **Grenade cycling:** Any bind that uses key-hold logic responds faster.
- **Strafing corrections:** Any rapid directional input benefits from a tighter response curve.

This setting costs players real time every session and almost nobody talks about it.
Set it once, test by holding a key in Notepad — it should start repeating almost instantly
and fire rapidly without gaps.

---

## 3. Power Plan

**Where:** Control Panel → Power Options  
*(Or search "Power plan" in Start)*

**Setting:** High Performance — or Ultimate Performance (see below)

Windows Balanced power plan throttles the CPU when it's not under heavy load, then ramps
it back up on demand. This sounds fine in theory. In practice it causes inconsistent frame
times — the CPU is not at full speed when the first few frames of a burst request it to be.

For a dedicated gaming machine, there is no reason to use Balanced.

### Enabling Ultimate Performance (recommended)

Ultimate Performance is a hidden Windows plan that disables all power-saving behaviors
entirely. Enable it once via PowerShell:

```powershell
powercfg -duplicatescheme e9a42b02-d5df-448d-aa00-03f14749eb61
```

After running this, it appears in Power Options. Select it and leave it.

**AMD CPUs:** Use AMD Ryzen High Performance or AMD's own balanced plan instead of
Ultimate Performance. AMD's power management is more tightly integrated with Ryzen's
boost behavior — Microsoft's Ultimate Performance plan can interfere with it.
Intel CPUs: Ultimate Performance is fine.

---

## 4. HAGS — Hardware-Accelerated GPU Scheduling

**Where:** Settings → System → Display → Graphics → Change default graphics settings  
**Setting:** Off on Pascal/Turing (10/20-series). Leave On on Ada (RTX 40-series) and Blackwell (RTX 50-series) if you use Reflex.

HAGS moves GPU scheduling work from the CPU to the GPU itself. In theory this reduces
CPU overhead. In practice on older architectures it introduces frame time inconsistency
in competitive games and can increase input latency in specific scenarios.

**The RTX 40-series and 50-series caveat:** NVIDIA's Reflex SDK depends on HAGS being
enabled to reach its lowest-latency path on Ada and Blackwell. CS2 ships Reflex.
If you turn HAGS off on a 40/50-series card, you give up the Reflex frame pacing
benefit that the driver was designed around. On those GPUs, leave HAGS on.

**On Pascal and Turing (GTX 10-series, RTX 20-series):** Turn it off. The latency
improvements promised by HAGS never materialized on those architectures and frame time
variance gets worse in CS2.

**On Ampere (RTX 30-series):** Test both. The hardware supports HAGS but the gain is
inconsistent depending on driver version. Run a baseline with it off, then on. Use
CapFrameX or HWInfo64 to compare frame times — not just average FPS.

The RTX 40-series case is documented empirically in
[exp-002](../experiments/exp-002-hags-rtx40-reflex.md): turning HAGS off on
an RTX 4090 with Reflex active dropped P1-Low by 43 %, Min by 65 %, and lifted
AdaptiveStd 11 %, while Average only fell 6 % — exactly the tail-collapse
pattern you'd expect when Reflex loses its lowest-latency pacing path.

---

## 5. NVIDIA App — 3D Settings for CS2

**Where:** NVIDIA App → Graphics → Program Settings → Counter-Strike 2  
(For display-level settings like G-Sync, use NVIDIA App → System → Display.)

NVIDIA retired the Classic NVIDIA Control Panel with Game Ready driver 610.47
(May 2026). On a clean install of any recent driver, only the NVIDIA App exists —
the right-click desktop entry is gone. The setting names below are unchanged from
the old Control Panel, only the click path has moved.

Per-program settings take priority over global — set them under Counter-Strike 2's
entry in Program Settings so other applications stay unaffected. If the game does
not appear in the list, click *Add* and browse to `cs2.exe`.

| Setting | Value | Why |
|---|---|---|
| Low Latency Mode | Ultra | Reduces pre-rendered frames in the driver queue — lower input lag |
| Power Management Mode | Prefer Maximum Performance | Forces GPU to stay at full clock, not throttle while waiting for frames |
| Vertical Sync | **On** on a G-Sync Compatible monitor with an FPS cap below refresh. **Off** on a fixed-refresh (non-VRR) monitor. | On a G-Sync window with a sub-refresh cap, NVCP V-Sync never engages — it only acts as a tearing safety net at the refresh ceiling. On a non-VRR display it adds blocking latency. |
| Texture Filtering – Quality | High Performance | Reduces GPU texture filtering work, minimal visual difference at CS2 distances |
| Threaded Optimization | Auto | Leave at Auto — forcing On or Off can cause issues depending on driver version |
| Max Frame Rate | Set to ~3% below refresh on G-Sync Compatible; Off on fixed-refresh | Driver-side cap is more consistent than `fps_max` in CS2, which has documented frametime interactions with the Reflex SDK. |

#### VSync note — the canonical G-Sync Compatible recipe

If you have a VRR display
(G-Sync Compatible, FreeSync over DisplayPort/HDMI), the configuration that gives
the lowest latency *and* zero tearing is:

- NVIDIA App V-Sync: **On**
- In-game V-Sync: **Off**
- NVIDIA Reflex (in CS2 video settings): **Enabled + Boost**
- Frame rate cap: **~3% below your refresh rate**, set in NVIDIA App's Max Frame Rate

This is the BlurBusters G-Sync 101 recipe, mirrored in NVIDIA's System Latency
Optimization Guide and confirmed by Valve's own in-game popup added in June 2024.
The math: on a 240Hz panel, cap at ~232. On a 360Hz panel, cap at ~349. On a 480Hz
panel, cap at ~465. The exact 3% offset keeps the framerate inside the VRR window
so V-Sync's blocking behavior never triggers — it only catches a frame at the
refresh ceiling, preventing the tear.

**Where to set the cap:** NVIDIA App's Max Frame Rate is more reliable than CS2's
`fps_max` in this role. The in-engine cap has documented frametime spike interactions
with the Reflex SDK on some driver versions. The driver-side cap is pre-render queue
and doesn't fight Reflex.

**On a fixed-refresh (non-VRR) monitor:** Turn NVIDIA App V-Sync **Off**, in-game
V-Sync **Off**, and leave `fps_max` uncapped (or set well above refresh). You'll see
tearing. That's the correct trade for the lowest possible latency on a non-VRR panel.
Do not enable V-Sync to hide tearing — the latency cost is real and measurable.

| Display type | NVIDIA App V-Sync | In-game V-Sync | Reflex | Frame cap |
|---|---|---|---|---|
| G-Sync Compatible / VRR | On | Off | Enabled + Boost | ~3% below refresh |
| Fixed-refresh, you accept tearing | Off | Off | Enabled | Off (uncapped) |
| Fixed-refresh, you cannot tolerate tearing | Off | On | Enabled + Boost | One frame below refresh |

#### When VRR inverts — FPS well below refresh

The G-Sync Compatible recipe
assumes your FPS lives near refresh. If your sustained FPS is well below
refresh (rule of thumb: FPS-to-refresh ratio below ~0.6), VRR's pacing
benefit can flip negative. The display refresh tracks an FPS that swings
through a wide range, which on OLED panels produces visible VRR Flicker
in dark UI regions, and on G-Sync Compatible monitors triggers driver-side
LFC (Low Framerate Compensation) frame-doubling on the deepest dips.

In that situation, switching to a **fixed refresh rate at the nearest
step above your 1%-Low** plus an `fps_max` matched to that refresh minus
a few frames produces smoother gameplay than the canonical VRR recipe,
even on a 540 Hz panel. See [exp-001](../experiments/exp-001-vrr-vs-fixed-refresh.md)
for the empirical case (AdaptiveStd 84 → 49, P1-Low 97 → 146 by switching
from 540 Hz + G-Sync + cap 525 to 270 Hz fixed + cap 263).

---

## 6. Fullscreen Optimizations

**Where:** Navigate to CS2.exe → Right-click → Properties → Compatibility tab  
**Setting:** Check *Disable fullscreen optimizations*

Also check: *Run this program as an administrator*

CS2.exe is typically at:  
`C:\Program Files (x86)\Steam\steamapps\common\Counter-Strike Global Offensive\game\bin\win64\cs2.exe`

Fullscreen Optimizations is a Windows feature that intercepts exclusive fullscreen
applications and runs them in a borderless-windowed mode instead, even if the game
requests exclusive fullscreen. This allows Alt-Tab to be faster but removes the
latency benefit of true exclusive fullscreen.

For CS2: disable it. You want exclusive fullscreen when the game requests it.

---

## 7. Everything else

These have smaller impact than the above but are worth doing once.

### Visual Effects — Adjust for best performance

**Where:** Search "Adjust the appearance and performance of Windows" in Start  
**Setting:** Select *Adjust for best performance*, then manually re-enable *Smooth edges of screen fonts*
(without this, text rendering looks broken)

This disables Windows animations, shadows, and transparency effects. CPU and GPU
cycles freed from rendering desktop animations are trivial in isolation — but they
add up, and the consistency gain is real.

### Xbox Game Bar and background recording

**Where:** Settings → Gaming → Xbox Game Bar — **Off**  
**Where:** Settings → Gaming → Captures → Background recording — **Off**

Game Bar overlays and background recording consume CPU and RAM even when not actively
recording. There is no reason to have this on if you're not using it.

### Startup programs

**Where:** Task Manager → Startup apps tab

Disable anything that doesn't need to run at login. Background processes compete for
CPU time during game sessions. Common culprits: Discord autostart (launch it manually),
software updaters, manufacturer utilities.

**Keep running:** NVIDIA app or GeForce Experience if you use its features. Steam.
**Disable:** Everything else you don't actively use during gaming.

### Windows notifications

**Where:** Settings → System → Notifications  
Or use Focus Assist (Windows 10) / Do Not Disturb (Windows 11) during sessions.

Notifications briefly pull focus and can cause micro-stutters. Turn off or suppress
during gaming sessions.

---

## 8. Core Isolation, Memory Integrity, and VBS

**Where:** Settings → Privacy & security → Windows Security → Device security → Core isolation → Memory integrity  
**Setting:** Off

This is the single biggest Windows-side FPS gain available in CS2 from a single toggle.

Virtualization-Based Security (VBS) and its child feature Memory Integrity (HVCI)
run parts of Windows inside a Hyper-V container so that even kernel-mode malware
cannot tamper with critical code. The security benefit is real. The performance
cost in CPU-bound games is also real — Tom's Hardware measured roughly an 8%
gaming penalty with VBS enabled, and CS2 is heavily CPU-bound on competitive
settings, so the hit lands hard.

Windows 11 24H2 ships with Memory Integrity *on by default* on clean installs.
Many players are paying the 8% tax without knowing it exists.

### How to verify VBS is actually off

Toggling Memory Integrity off in the UI is not always enough. Press Win+R, run
`msinfo32`, and scroll to the bottom of the *System Summary* page.

- **Virtualization-based security:** must read *Not enabled*.
- If it reads *Running* or *Enabled*, VBS is still active even if the Memory
  Integrity toggle shows Off.

If VBS is still running after toggling Memory Integrity off, the cause is almost
always one of the Windows features that depends on Hyper-V. Open *Turn Windows
features on or off* and uncheck:

- Hyper-V (all sub-items)
- Virtual Machine Platform
- Windows Hypervisor Platform
- Windows Sandbox
- Windows Subsystem for Linux (only if you do not use WSL)

Reboot. Re-check `msinfo32`. It should now read *Not enabled*.

**What you lose:** Memory Integrity protection against kernel-mode rootkits,
the ability to run WSL2 / Docker Desktop / Windows Sandbox / Hyper-V VMs. If
you use any of those tools for work, keep VBS on and accept the FPS cost.

**What you do not lose:** VAC, Steam, normal antivirus, normal Windows updates,
Game Mode, Reflex, none of which depend on VBS.

---

## Summary — what to do in order

| Priority | Setting | Where |
|---|---|---|
| 1 | Enhanced Pointer Precision off, pointer speed 6/11 | Mouse settings → Pointer Options |
| 2 | Keyboard repeat delay short, repeat rate fast | Control Panel → Keyboard |
| 3 | Power plan: Ultimate Performance (or AMD High Performance) | Control Panel → Power Options |
| 4 | HAGS: off on 10/20-series, on for RTX 40/50-series Reflex | Settings → Display → Graphics |
| 5 | NVIDIA App: Low Latency Ultra, Power Max, V-Sync **On** for G-Sync + cap ~3% below refresh (Off on non-VRR) | NVIDIA App → Graphics → Program Settings |
| 6 | Disable fullscreen optimizations on cs2.exe | cs2.exe → Properties → Compatibility |
| 7 | Visual effects: adjust for performance | System Properties → Advanced |
| 7 | Xbox Game Bar off, background recording off | Settings → Gaming |
| 7 | Startup programs — audit and trim | Task Manager → Startup |
| 8 | Memory Integrity / VBS off (verify with `msinfo32`) | Windows Security → Device security |

---

← [Back to compendium](../README.md) → [Settings](../settings/README.md)
