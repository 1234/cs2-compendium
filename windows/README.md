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
**Setting:** Off

HAGS moves GPU scheduling work from the CPU to the GPU itself. In theory this reduces
CPU overhead. In practice it introduces frame time inconsistency in competitive games
and can increase input latency in specific scenarios.

CS2 does not benefit from HAGS on. Turn it off.

If you have a new GPU (RTX 40-series or later) and genuinely want to test it:
run with it off first, establish a baseline, then test on. Use CapFrameX or
HWInfo64 to compare frame times — not just average FPS.

---

## 5. NVIDIA Control Panel

**Where:** Right-click desktop → NVIDIA Control Panel → Manage 3D Settings  
Set globally under *Global Settings*, or per-program under *Program Settings* for CS2 specifically.

Per-program settings take priority over global — use program settings for CS2 if you
want to keep other applications unaffected.

| Setting | Value | Why |
|---|---|---|
| Low Latency Mode | Ultra | Reduces pre-rendered frames in the driver queue — lower input lag |
| Power Management Mode | Prefer Maximum Performance | Forces GPU to stay at full clock, not throttle while waiting for frames |
| Vertical Sync | Off | VSync in the driver adds latency — disable globally, disable in-game |
| Texture Filtering – Quality | High Performance | Reduces GPU texture filtering work, minimal visual difference at CS2 distances |
| Threaded Optimization | Auto | Leave at Auto — forcing On or Off can cause issues depending on driver version |
| Max Frame Rate | Off | Control this in-game with `fps_max` instead |

**VSync note:** With VSync off and fps_max uncapped, you may see screen tearing.
This is normal and generally preferable to the latency VSync adds.
If tearing bothers you: use `fps_max` set slightly below your monitor's refresh rate
(e.g., `fps_max 537` for a 540Hz monitor) rather than enabling VSync.

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

## Summary — what to do in order

| Priority | Setting | Where |
|---|---|---|
| 1 | Enhanced Pointer Precision off, pointer speed 6/11 | Mouse settings → Pointer Options |
| 2 | Keyboard repeat delay short, repeat rate fast | Control Panel → Keyboard |
| 3 | Power plan: Ultimate Performance (or AMD High Performance) | Control Panel → Power Options |
| 4 | HAGS off | Settings → Display → Graphics |
| 5 | NVIDIA: Low Latency Ultra, Power Max, VSync off | NVIDIA Control Panel |
| 6 | Disable fullscreen optimizations on cs2.exe | cs2.exe → Properties → Compatibility |
| 7 | Visual effects: adjust for performance | System Properties → Advanced |
| 7 | Xbox Game Bar off, background recording off | Settings → Gaming |
| 7 | Startup programs — audit and trim | Task Manager → Startup |

---

← [Back to compendium](../README.md) → [Settings](../settings/README.md)
