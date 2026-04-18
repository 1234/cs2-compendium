# Settings

CS2-specific configuration — every option explained, not just listed.

Three parts: **Video settings**, **Launch options**, **autoexec.cfg**.

---

## Video Settings

### Basic

| Setting | Recommended | Why |
|---|---|---|
| **Aspect Ratio** | 4:3 or 16:9 | Personal choice — see note below |
| **Resolution** | 1280×960 (4:3) or 1920×1080 (16:9) | Depends on aspect ratio choice |
| **Display Mode** | Fullscreen | Exclusive fullscreen = lowest input latency. Fullscreen Windowed lets Windows interfere. |
| **Refresh Rate** | Max your monitor supports | Always set to max. There is no reason to cap it here. |
| **Brightness** | 100–110% | No performance impact. Set to where enemy models are clearest against backgrounds. |
| **Color Mode** | Computer Monitor | TV mode applies a different gamma curve designed for television displays — washes out the image for monitor use. Always Computer Monitor. |
| **Laptop Power Savings** | Disabled | Throttles GPU performance. Off even on a laptop. |

**Resolution and aspect ratio — the actual tradeoff:**

4:3 stretched (e.g., 1280×960) stretches player models horizontally — they become wider,
which some players find easier to hit. It also runs at lower pixel count, which often
improves FPS on CPU-limited machines. The downside: slightly reduced horizontal FOV
and visual distortion on objects that aren't players.

16:9 native (1920×1080) gives maximum visual clarity, widest FOV, sharpest image.
If your PC can maintain high FPS at native resolution, there's no performance argument
for 4:3.

Neither is objectively correct. Both are used at the highest levels. Pick one and
don't switch — consistency builds muscle memory, switching resets it.

---

### Advanced Video

CS2 is CPU- and RAM-bottlenecked, not GPU-bottlenecked. This means your GPU almost
always has spare capacity. The practical implication: settings that only load the GPU
(MSAA, shadows, ambient occlusion, texture filtering) often cost you nothing measurable
in FPS — because the CPU was the ceiling, not the GPU. Test each setting by watching
GPU frametime, not just average FPS.

| Setting | Recommended | Why |
|---|---|---|
| **Wait for Vertical Sync** | Disabled | VSync syncs framerate to refresh rate to eliminate tearing, but adds 1–2 frames of input lag. Off for competitive. |
| **Multisampling Anti-Aliasing (MSAA)** | 8× if hardware allows | See note below |
| **Global Shadow Quality** | High or Very High | **Not Low or Very Low** — shadow information is gameplay-relevant. See dynamic shadows note. |
| **Dynamic Shadows** | All | Real-time shadows from player models and objects. Separate from Global Shadow Quality since June 2024. Options: *Sun Only* (static light only) or *All* (full dynamic shadows). Set to All — enemy shadows under doors and around corners are gameplay information. |
| **Model / Texture Detail** | High | Low makes player models visually coarser and harder to read at distance. |
| **Texture Filtering Mode** | Anisotropic 16× | Keeps textures sharp when viewed at angles (floors, walls). GPU cost on modern hardware is negligible. |
| **Shader Detail** | Low | Surface reflections and material effects. No competitive impact. Low = best performance. |
| **Particle Detail** | Low or Medium | Controls complexity of explosion, smoke, and impact particles. Lower = less visual noise during firefights. No argument for High in competitive. |
| **Ambient Occlusion** | Disabled or Medium | Contact shadows in corners and against surfaces. Disabled = best performance. Medium if GPU has headroom and you prefer the visual depth. No direct gameplay impact. |
| **Boost Player Contrast** | Enabled | Makes player silhouettes more distinct from backgrounds. Free visibility improvement — no performance cost. |
| **Render Multiprocessing** | Enabled | Allows CS2 to distribute rendering work across CPU threads. Always on. |
| **High Dynamic Range** | Quality if GPU allows, Performance otherwise | HDR affects lighting calculation quality — a GPU task. Since CS2 is CPU-bottlenecked, the GPU usually has capacity for Quality. Test: if GPU frametime doesn't increase, use Quality. |
| **FidelityFX Super Resolution (FSR)** | Disabled | FSR renders at lower resolution and upscales. Upscaling adds blur. Blur kills visual clarity. Off. |
| **NVIDIA Reflex Low Latency** | Enabled + Boost | Highest-impact setting in this section — see note below. |

**MSAA — why max is the right call when hardware allows:**

MSAA smooths jagged edges by rendering geometry at higher sample counts. It's a GPU-only
workload. In CS2, where the CPU is the bottleneck, your GPU is often sitting at 50–70%
utilization. That spare capacity is doing nothing — MSAA uses it.

Higher MSAA = sharper, cleaner edges = player models and angles are visually cleaner to
read. Pros including donk have noted in interviews they push MSAA to max when hardware
doesn't limit them. If your framerate stays stable at 8×, there is no argument for lower.

**How to test:** Set to 8×. Play a match. If average FPS and frametimes are unchanged,
keep it. If you see drops, step down to 4×. The moment you see no difference in performance
is the floor you should stay at.

**NVIDIA Reflex — why it matters:**

Without Reflex, the CPU pre-renders frames and queues them in a GPU buffer. This render
queue adds latency — your mouse input waits for frames already in the pipeline before
your input reaches the screen.

Reflex eliminates the queue. The CPU only submits a frame when the GPU is ready for it.
Your inputs reach the screen faster — measurably.

**Enabled + Boost** slightly raises GPU clock to prioritize latency over power efficiency.
On a gaming PC the power cost is irrelevant. Leave it on.

*AMD GPU: Reflex is NVIDIA-only. Enable AMD Anti-Lag in AMD Software — same principle.*

---

## Launch Options

Set in Steam: right-click CS2 → Properties → Launch Options.

### Use these

| Option | What it does |
|---|---|
| `-novid` | Skips the intro video on launch. No downside. |
| `-console` | Opens the developer console automatically on launch. |
| `-nojoy` | Disables joystick/gamepad input detection. Effect in CS2 unconfirmed — harmless to include. |
| `-fullscreen` | Forces fullscreen mode. |
| `-w 1280 -h 960` | Forces resolution (adjust to your choice). |
| `+exec autoexec.cfg` | Executes your autoexec on launch. CS2 should do this automatically, but this guarantees it. |

**Example launch string:**
```
-novid -console +exec autoexec.cfg
```

### Do not use these

These are CS:GO launch options that still circulate but do nothing or cause problems in CS2:

| Option | Why it's dead |
|---|---|
| `-d3d9ex` | CS:GO DirectX 9 optimization. CS2 runs on DX11/Vulkan. Ignored. |
| `-threads [n]` | CS:GO CPU threading option. CS2 handles threading internally. |
| `-tickrate 128` | Only affects local servers you host yourself. Has no effect on matchmaking. |
| `+fps_max 0` | Put this in autoexec.cfg instead — it belongs in config, not launch options. |
| `-freq [hz]` / `-refresh [hz]` | CS2 auto-detects refresh rate from your OS display settings. These parameters are largely ignored — set the refresh rate correctly in Windows instead. |
| `-high` | A Valve developer confirmed this makes performance worse in CS2, not better. Process priority for CS2 is better managed through Windows Task Manager if needed. |

---

## autoexec.cfg

The autoexec runs every time CS2 launches. It's the right place for any setting you
always want active — binds, network config, FPS cap, viewmodel, radar.

**Location:** `...\Steam\steamapps\common\Counter-Strike Global Offensive\game\csgo\cfg\autoexec.cfg`

If the file doesn't exist, create it. If it does, CS2 runs it automatically on launch.
You can also force it with `+exec autoexec.cfg` in launch options.

**→ Reference file: [autoexec.cfg](autoexec.cfg)**

The file is fully commented — every line has a note explaining what it does and why.
Copy it, adjust to your preference, drop it in your cfg folder.

**Comment syntax:** CS2 cfg files support both `//` (official Source engine standard)
and `#` (also accepted by the parser). Both work. Use whichever you prefer — the game
doesn't care.

**Commented-out binds:** Some binds in the reference file are commented out by default
because they conflict with other binds depending on mode. The KZ `!undo` bind and
NADR training binds share keys with other functions — uncomment the ones you need
when switching to that mode.

---

### Command reference — what each setting actually does

**Mouse input**

`m_rawinput 1` — Forces CS2 to read mouse movement directly from the hardware driver,
completely bypassing Windows input processing. With raw input on, Windows mouse settings
(pointer speed, Enhanced Pointer Precision) have no effect on in-game movement.
This should always be 1. CS2 saves it to the user config, but having it explicit in
the autoexec guarantees it survives config resets.

---

**Sensitivity**

`zoom_sensitivity_ratio_mouse 1.0` — Multiplier applied to your sensitivity when scoped
(AWP, Scout, SG553, AUG). A value of 1.0 means your scoped sensitivity is identical to
your hip-fire sensitivity. The default is 1.0 — setting it explicitly means you always
know what's active and can adjust intentionally if the scope ever feels wrong.

---

**FPS**

`fps_max 0` — Removes the FPS cap entirely. More frames = lower latency between input
and display. The game's default cap (300) is too conservative for high-refresh monitors.

`fps_max_menu 60` — Caps FPS on the main menu only. The menu renders at full speed by
default, which wastes GPU power and generates heat for no benefit.

---

**Network**

`rate 786432` — The maximum rate (bytes/sec) CS2 will accept data from the server.
786432 is the correct maximum value. Setting it lower throttles your connection to the
server. Always set to max unless you have bandwidth constraints.

`cl_cmdrate 128` / `cl_updaterate 128` — In CS2's sub-tick system, these values are
enforced server-side and your client settings are ignored in matchmaking. They have no
effect on Valve servers. They remain relevant only for locally hosted servers.
They are kept in the autoexec as documentation and for local server use — having them
set to 128 does no harm.

`cl_interp 0` — Interpolation delay. CS2 interpolates the visual positions of other
players between server updates to smooth out movement. Setting to 0 lets the server
calculate the minimum safe value based on your connection quality — the lowest
interpolation the network can reliably support.

`cl_interp_ratio 1` — Multiplier on the interpolation window. Default is 2, which
doubles the interpolation delay. Setting to 1 halves it — the visual representation
of other players is closer to their actual server position. Lower = less lag compensation
buffer, tighter feel. Used by most competitive players.

---

**Radar**

`cl_radar_always_centered 0` — The most important radar setting. With it off, your
position moves on the radar while the map stays fixed — you always know where A, B,
and CT are relative to the map layout. With it on (default), the map rotates around
you, which makes reading rotations and teammate positions significantly harder.

`cl_radar_scale 0.3` — How zoomed in the radar is. Lower = more of the map visible at
once. Good default for most maps. On large maps like Nuke or Overpass, try 0.25.
On smaller maps, 0.35–0.4 makes individual positions easier to read.

`cl_hud_radar_scale 2` — Physical size of the radar on screen. 1 = default (small).
Larger values make the radar easier to glance at without losing focus on the game.

`cl_hud_radar_background_alpha 0.8` — Opacity of the radar background. 1.0 = fully
opaque black background. Lower values make the background more transparent, which some
players find less visually intrusive. 0.8 is a slight reduction from the default (1.0).

`cl_radar_square_always 0` — Sets the radar to round shape (0) instead of square (1).
Round is the default appearance. Personal preference only — no gameplay difference.

---

**Sound**

`snd_voipvolume 1.3` — Volume of incoming voice chat relative to game audio. Default
is 1.0. CS2's game sounds (gunshots, footsteps, grenades) are mixed loud by design —
in hectic rounds they can easily drown out teammate calls. Raising this slightly ensures
you don't miss critical information mid-fight. Lower it if voice feels too dominant
over game audio. Adjust to taste — there is no universally correct value.

---

**Crosshair**

`cl_crosshair_recoil 1` — Makes the crosshair visually track the recoil pattern when
spraying. Useful when learning sprays — you can see where your shots are actually going.
Once you've internalized a weapon's spray, some players prefer `0` so the crosshair
stays static and they control recoil by feel rather than chasing the crosshair.

---

**Viewmodel**

The viewmodel controls how much of your screen the weapon occupies. There is no
universally correct setting — it's a visibility vs. preference tradeoff.

The reference values (`fov 68`, `offset_z -2`) push the weapon smaller and lower than
default, opening up more of the center FOV.

Actual defaults: `viewmodel_fov 60`, `offset_x 1`, `offset_y 1`, `offset_z -1`.
Valid ranges: FOV 54–68, offsets -2.5 to 2.5 (X), -2 to 2 (Y/Z).

The reference values move toward minimum screen coverage compared to default.
Some players prefer the default or something in between. The only wrong answer is
leaving it wherever it landed without having made a conscious choice.

---

**Grenade binds**

CS2's default weapon slot system requires cycling through grenades with a single key.
Dedicated binds (one key per grenade type) mean you always pull exactly the right
grenade without cycling.

Slot reference: slot6 = HE, slot7 = flash, slot8 = smoke, slot9 = decoy,
slot10 = molotov/incendiary.

---

## Commands that don't work — what gets passed around but shouldn't

These appear in guides, config packs and Reddit threads. They either do nothing in CS2
or are left over from CS:GO where they had a different meaning.

| Command | Why it's dead |
|---|---|
| `cl_forcepreload 1` | CS:GO asset preloading command. CS2 handles asset loading internally — this does nothing. |
| `mat_queue_mode 2` | CS:GO multithreading flag. Not applicable to CS2's rendering architecture. |
| `r_dynamic 0` | Disabled dynamic lighting in CS:GO. CS2 rebuilt the lighting system — command has no meaningful effect. |
| `cl_predictweapons 1` | Already the default. Setting it explicitly changes nothing. |
| `cl_lagcompensation 1` | Already the default. Same as above. |
| `fps_max 999` | Functionally identical to `fps_max 0` unless your GPU produces 999+ FPS. Use `fps_max 0`. |
| `-threads [n]` in launch options | CS:GO CPU threading override. CS2 manages threading internally. |
| `rate 128000` | Outdated value from older CS:GO guides. Correct maximum is `786432`. Lower values throttle your connection. |
| `cl_bob_lower_amt` / `cl_bobamt_lat` / `cl_bobamt_vert` | **Removed from CS2.** Worked in CS:GO — Valve deliberately removed them to standardize viewmodel bob across all players. `cl_usenewbob` was briefly added in February 2024 then also removed. Console ignores these commands silently — no ban risk, but no effect either. |

**On CS:GO configs copied wholesale into CS2:**  
Many circulating "pro configs" are CS:GO autoexecs with CS2 commands added on top.
The dead commands above are harmless — they're ignored. But they create noise and
make it harder to understand what your config is actually doing. Keep your autoexec
clean and only include commands you understand.

---

← [Back to compendium](../README.md) → [Training](../training/README.md)
