# Peripherals

> This section is being built out. The myths and click registration content below
> is documented and complete — the full deep-dives per peripheral are coming.

Topics planned for this section:

- Mouse — sensor, shape, weight, grip styles (palm/claw/fingertip)
- Wireless vs wired — the 2025 verdict
- Polling rate — what it actually does and when it matters
- Hardware debounce — what it is, how to adjust it
- Mousepads — surface types, size, why it matters
- Keyboard — switch types, actuation force, polling rate
- Headset — open vs closed, HRTF, what to look for
- Mouse bungee — when it matters and when it doesn't

---

## Myths — what doesn't affect your shooting

### Windows double-click speed

> *"Increase double-click speed — your shots come out faster and repeat better."*

**Not true.**

Double-click speed is a Windows UI setting (Control Panel → Mouse → Buttons tab).
It controls how quickly two consecutive clicks need to happen for Windows to register
them as a double-click — relevant for desktop interactions like opening folders.

It has no effect on CS2 for two reasons:

**1. CS2 uses raw input.**  
With `m_rawinput 1` (which should always be on), CS2 reads mouse input directly from
the hardware driver and bypasses the Windows input stack entirely. Windows mouse
settings — including double-click speed, pointer speed, and Enhanced Pointer Precision —
are not visible to the game.

**2. Fire rate is a server-side game mechanic.**  
Weapon fire rate in CS2 is a hard-coded value per weapon. AK-47: 600 RPM.
M4A4: 666 RPM. No Windows setting, no peripheral setting, nothing on your machine
changes this. It is enforced by the game server.

**Why this myth exists:**  
Placebo effect plus misunderstanding. Someone changes a setting, plays a good session,
draws the wrong conclusion. The "tip" spreads because it sounds plausible — and nobody
who shares it explains how raw input actually works.

---

## What actually affects click registration in CS2

| Factor | What it does | Where to configure |
|---|---|---|
| **Mouse polling rate** | How often the mouse reports its position to the PC — 1000Hz = 1000 reports/sec. Higher polling = more precise timing on inputs | Mouse software (e.g. Razer Synapse, Logitech G Hub) or DIP switch on mouse |
| **Hardware debounce** | A delay built into the mouse switch to prevent accidental double-clicks from switch bounce. Too much debounce = delayed click registration | Mouse software — some mice allow reducing debounce delay |
| **CS2 sub-tick system** | CS2 registers inputs between ticks, not only at tick boundaries. This means click timing is more precise than 64-tick CSGO, but depends on how the engine processes the input at the moment it arrives | No user configuration — understanding it helps set expectations |
| **USB port / connection** | A polling rate of 1000Hz requires a USB port that can handle it reliably. USB 2.0 is fine for 1000Hz. For 2000Hz+ mice, use USB 3.0 directly on the motherboard | Physical connection |

Full deep-dives into each of these coming in this section.

---

← [Back to compendium](../README.md)
