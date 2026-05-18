# Mouse Grip

Most guides classify grip into three boxes: palm, claw, fingertip. Pick one, the story goes.

That model hasn't matched what top CS2 pros actually do for years.
The current professional grip is a hybrid, and the choice between palm-leaning and hover-leaning
is no longer cosmetic. It changes which kind of aim you're good at.

This section explains the actual mechanics, the trade-off most guides skip, and how to find
the grip that fits you and your hardware.

---

## The three traditional grips

These are the textbook definitions. They are the starting point, not the finish.

### Palm grip
Entire hand rests on the mouse. Palm makes full contact with the rear hump.
Fingers lie relatively flat along M1/M2. Maximum contact area, maximum stability.
Movement comes primarily from the shoulder, elbow, and wrist. Fingers do little.

### Claw grip
Palm makes light contact at the rear of the mouse. Fingers are arched (clawed),
contact only at the fingertips. Maximum finger mobility, reduced contact area.
Movement is shared between arm and fingers, with fingers handling rapid micro-adjustments.

### Fingertip grip
No palm contact at all. Only fingertips touch the mouse. Minimum contact, maximum finger control.
Movement is almost entirely fingers and wrist. Rare in competitive CS2: it doesn't scale
to the precision demands of low-sensitivity play.

### Why the textbook classification breaks down

Look at any current professional CS2 handcam. You won't see clean examples of any of these.
You'll see hybrids: palm contact that isn't a full palm, fingers arched without being a true claw,
forearm anchored to the desk in ways that change what each grip even means.

The traditional three-grip model describes hand-on-mouse geometry. It ignores the rest of the arm.
For CS2, where most pros run sensitivity low enough that the arm does substantial work,
that's the part that actually determines aim outcomes.

---

## The current professional standard: Hover Palm/Claw Hybrid

Matthew Hwu (DPT, OCS, CSCS), esports physiotherapist at 1HP, published a frame-by-frame
analysis of top CS2 grips in May 2024. His finding: **Donk, m0NESY, ZywOo, and Twistzz all
use a variant of the same grip.** He calls it Hover Palm/Claw Hybrid.

### What it actually looks like

- Finger contact points are shifted **forward** on the mouse, closer to the front
- Palm makes light contact with the rear of the mouse, not full pressure, not floating
- Fingers are slightly arched, not flat
- The wrist sits relatively low, close to the pad surface
- Forearm rests on the desk or pad, not floating

### The biomechanical claim

Hwu's argument: shifting finger contact forward moves the center of mass forward,
which **reduces the distance between the wrist joint and the optical sensor.** The wrist
is the primary control point for fine adjustments. Bringing it closer to the sensor means
small wrist movements translate to more direct cursor responses.

The light palm contact preserves a stable reference point. You know where your hand is,
without locking the hand into the mouse, which would restrict finger movement.

### Forearm placement: on the desk, not floating

This is the part most online discussions get wrong. Hwu makes the distinction explicit:
the Hover Palm/Claw works **only with the forearm anchored.** A floating forearm with this
grip overworks the extensor muscles and produces tension and inconsistency under load.

Anchoring the forearm makes the elbow (or upper forearm) the pivot for large swipes,
and the wrist/fingers the pivot for micro-adjustments. The forearm stays planted across
both motion scales.

---

## The opposite pole: Full Standard Palm with Forearm Anchor

The Hover Palm/Claw is dominant among the top tier, but not universal. The clearest
counter-example is **NiKo**, who runs an extreme low sensitivity and uses a traditional
Standard Palm with full forearm contact on the desk. His pivot for almost all aiming
is the elbow. The forearm functions as a single rigid unit, and the wrist barely moves.

This works because:
- The elbow as a pivot is mechanically very stable. Small angular changes produce predictable cursor distances.
- Full palm contact eliminates finger micro-tremor from the equation.
- At very low sensitivity, the cursor distance you need for micro-adjustments is large enough that you don't need finger precision.

Standard Palm with Forearm Anchor at low sensitivity is the other valid endpoint.
The trade-off it accepts is the topic of the next section.

---

## The trade-off most guides don't name

This is the part that determines which grip is right for you, and it has nothing to do
with hand size or comfort.

### More palm contact = better static micro-aim, worse dynamic centering

Full palm contact gives you a high-friction, high-inertia connection between hand and mouse.
That stability is excellent when you're holding an angle and making tiny corrections.
Your crosshair doesn't drift, micro-adjustments are precise, you can settle on a pixel.

But the same friction and inertia work against you during sustained movement:
strafing, counter-strafing while tracking, re-centering after a swipe, KZ and surf-style
continuous airstrafing. The hand has to overcome more drag to keep the cursor flowing.

### More hover = better dynamic centering, more micro-jitter

Lifting the palm slightly reduces friction. The hand moves more freely across the pad,
and continuous tracking becomes smoother. Movement-heavy play feels more responsive.

The cost: less mechanical stability. Without the palm anchor, finger micro-tremor
shows up as small cursor jitter on static targets. This is **trainable**. It builds out
over weeks of pinky-scratch anchoring and smoothness drills, but it's the entry cost.

### The trade-off in one sentence

Palm contact trades dynamic mobility for static stability. Hover trades static stability
for dynamic mobility. Most players don't notice the trade-off until they actually compare
both, and then it's obvious, and consistent with what the biomechanics predict.

If you find your micro-aim is good but you struggle with movement-centering: you're palm-leaning.
If your movement is smooth but you twitch on static targets: you're hover-leaning.
Both are valid. The right answer depends on what you play.

---

## The second axis: arm-aim vs wrist-aim

Palm vs hover describes how much of your hand touches the mouse. Arm-aim vs wrist-aim
describes where your pivot is. These two axes are independent. You can combine them in
any way: hover with arm-aim, palm with wrist-aim, anything in between.

Understanding the second axis is what separates intuitive grip choice from informed grip choice.

### Definitions

**Arm-aim** uses the elbow (or upper forearm) as the primary pivot. The forearm moves
across the desk. The wrist stays relatively still. Movement originates from large muscle
groups: shoulder, upper arm, elbow.

**Wrist-aim** uses the wrist joint as the primary pivot. The forearm anchors. The hand
rotates around the wrist. Movement originates from forearm muscles via wrist flexion,
extension, and ulnar-radial deviation.

**Hybrid** uses both. Different motion scales activate different pivots: arm for large
swipes, wrist for medium adjustments, fingers for fine corrections. Most pros are obligate
hybrids whether they describe themselves that way or not.

### The mechanical reality: sensitivity forces the choice

This is the part that most guides treat as preference and is actually math.

The wrist has roughly 70 degrees of rotation in each direction. That maps to about
10 to 15 cm of arc at the optical sensor. Beyond that distance, the wrist runs out of
range of motion. Something else has to provide the movement.

The eDPI formula (eDPI = DPI × in-game sensitivity) maps directly to centimeters per
180 degree turn. Examples at 400 DPI:

| eDPI | cm per 360° | cm per 180° | Wrist alone? |
|---|---|---|---|
| 400 | ~91 cm | ~46 cm | Impossible |
| 800 | ~46 cm | ~23 cm | Insufficient |
| 1000 | ~37 cm | ~18 cm | Borderline |
| 1500 | ~24 cm | ~12 cm | Viable |
| 2000 | ~18 cm | ~9 cm | Comfortable |

At low eDPI, arm engagement is not a stylistic choice. It is mechanically required.
The wrist cannot cover the distance. This is why low-sens players cluster around arm-aim
and high-sens players cluster around wrist-aim. The causality runs from sensitivity to
pivot choice, not the other way around.

### What current CS2 pros actually run (May 2026)

Verified eDPI values from ProSettings:

| Player | DPI | Sens | eDPI | cm/360° | Classification |
|---|---|---|---|---|---|
| NiKo | 800 | 0.7 | 560 | ~65 cm | Obligate arm-aim (extreme) |
| ZywOo | 400 | 2.0 | 800 | ~46 cm | Arm-dominant hybrid |
| m0NESY | 400 | 2.3 | 920 | ~40 cm | Arm-dominant hybrid |
| Twistzz | 800 | 1.2 | 960 | ~38 cm | Arm-dominant hybrid |
| donk | 800 | 1.25 | 1000 | ~37 cm | Wrist-leaning hybrid |
| EliGE | 1600 | 0.74 | 1184 | ~31 cm | Balanced hybrid |

The median pro CS2 eDPI sits around 880 (897 tracked pros, May 2026). Seventy percent
fall between 700 and 1200. That clustering is where wrist plus arm hybrid is mechanically
viable. Below 700, the arm dominates by necessity. Above 1500, the wrist is enough on
its own.

### The orthogonality with grip

| Grip | Compatible with arm-aim | Compatible with wrist-aim |
|---|---|---|
| Full Palm | Yes (NiKo's combination) | No, palm contact restricts wrist rotation |
| Hover Palm/Claw | Yes (pro standard) | Yes, hover frees the wrist |
| Claw | Yes | Yes |
| Fingertip | Yes but suboptimal | Yes, maximum wrist mobility |

The professional CS2 standard sits at the intersection of Hover Palm/Claw grip plus
arm-dominant hybrid aim. That combination unlocks both: stable arm sweeps for big
movements and free wrist for refinement. NiKo's combination (Full Palm plus extreme
arm-aim) is the other documented endpoint, viable at extreme low sensitivity where the
wrist is barely used anyway.

### Trade-offs by weapon

Arm-aim is mechanically better at:
- AWP holds and pre-aiming (stability dominates, micro-flicks are rare)
- Sustained spray control (the long downward arc of an AK pattern is an arm motion)
- Long-range engagement (every shot is a small adjustment from a held angle)

Wrist-aim is mechanically better at:
- Deagle one-tapping (sub-200ms flick reactivity)
- Close-quarters dueling (small adjustments at speed)
- Movement-heavy play where dynamic re-centering matters

Hybrid is required for everything else. CS2's map design and weapon variety reward
balanced engagement at the 700 to 1200 eDPI range, which is exactly the band where
obligate hybrid sits. That is not coincidence. The professional consensus reflects
where the math allows the broadest play.

### What this means for grip selection

The trade-off in the previous section (palm vs hover) tells you which contact style fits.
This axis tells you which pivot fits. Your sensitivity, your monitor size, your pad size,
and what weapons you play most should constrain your eDPI before you commit to a grip.

If you run sub-600 eDPI: arm-aim is forced. Palm or hover both work, but palm with
forearm anchor (NiKo's combination) is the more stable choice.
If you run 700 to 1200 eDPI: hybrid is forced. Hover Palm/Claw is the dominant
combination because it frees the wrist without giving up arm stability.
If you run above 1500 eDPI: wrist-aim is viable. Claw or fingertip grips become
more attractive.

You don't pick aim style. You pick sensitivity. Aim style follows. Get sensitivity
wrong and no grip will compensate.

---

## What the pros actually use (May 2026)

Mouse choice is downstream of grip. Pros pick mice that fit the grip, not the other way around.
Current verified setups for top CS2 pros:

| Player | Mouse | Form factor |
|---|---|---|
| **donk** | ZOWIE prototype (unreleased; signature edition in development) | Symmetrical, low hump. Switched at BLAST Bounty Winter 2026 from G Pro X Superlight |
| **m0NESY** | Logitech G Pro X2 SUPERSTRIKE | Symmetrical, ~125mm, HITS clicks |
| **ZywOo** | VAXEE OUTSET AX Wireless (custom yellow build, not a public signature edition) | Symmetrical, comfortable mid-hump |
| **Twistzz** | Razer Viper V4 Pro | Symmetrical |
| **NiKo** | Razer DeathAdder V4 Pro NiKo Edition | Ergonomic (right-handed), tall hump |

The pattern: every Hover Palm/Claw user is on a symmetrical mouse with a mid-or-lower hump.
The one major Standard Palm user (NiKo) is on an ergonomic mouse with a tall right-shifted hump.

This is not coincidence. The grip you can run is constrained by the mouse shape.

---

## Mouse shape determines viable grip

A mouse is a piece of hardware whose curves push your hand into specific positions.
Fighting that geometry is a tension source: small, constant, ergonomically expensive.

### Ergonomic (right-handed) mice
DeathAdder shape: 125-130mm long, hump shifted right and back, designed for palm contact.
Examples: Razer DeathAdder V4 Pro, ZOWIE EC2-DW, Logitech G Pro X Superlight 2 DEX.

The shape gives you a built-in palm rest. Hover-Palm grip on an ergonomic mouse is awkward.
The rear hump pushes into your palm muscles when you try to lift the palm. The mouse
is fighting your grip the whole time.

**Compatible grips:** Standard Palm (NiKo's style), Relaxed Claw.
**Incompatible:** Pure Hover Palm/Claw. The geometry actively resists it.

### Symmetrical (ambidextrous) mice
Centered, often lower hump. Examples: Logitech G Pro X Superlight 2, Pulsar X2,
VAXEE OUTSET AX, Razer Viper V4 Pro.

The hump in the middle gives you palm contact without committing your hand to a full palm rest.
Light palm contact at the rear stays light. Fingers can shift forward without losing reference.

**Compatible grips:** Hover Palm/Claw Hybrid, Claw, Fingertip.
**Less ideal for:** Full Standard Palm (some shapes lack the rear support a palm-rester needs).

### Low-hump symmetrical mice
Sub-category of symmetrical, designed for hover-style grips. The most explicit example:
ZOWIE FK2-DW Wireless, which ZOWIE markets specifically for claw and fingertip grips.
Lower profile means more space between palm and shell, making the hover natural rather than forced.

**Compatible grips:** Hover Palm/Claw, Claw, Fingertip.
**Not compatible:** Full palm. ZOWIE explicitly states the FK2-DW is unsuitable for palm-dominant users.

### Practical implication

If your grip style and your mouse shape are misaligned, you will feel constant low-grade tension
even if you can't articulate why. The mouse is asking your hand to rest, your grip is asking
your hand to hover. Something has to give, usually your forearm muscles, and over months,
your tendons.

---

## Hand size and mouse size

Shape determines which grips are viable. Size determines whether the chosen grip actually
functions on that shape. A symmetrical mid-hump mouse can be the right form factor and the
wrong size simultaneously. The result is the same chronic tension as a shape mismatch.

### How to measure your hand

The competitive standard uses two measurements taken on the dominant hand:

**Hand length:** From the wrist crease (the first horizontal crease at the base of the palm,
not the heel of the hand) to the tip of the middle finger. Hand flat on a table, fingers
relaxed, neither stretched nor curled. Take the measurement in millimeters.

Why the wrist crease specifically: it is the anatomical landmark defined in ISO 7250-1
(the standard mouse manufacturers use for size guides). Measuring from the heel of the
hand undersizes by 5 to 10 mm and breaks every formula below.

**Hand width:** Across the knuckles at the base of the four fingers (excluding the thumb).
This is the widest part of the relaxed palm.

Record as "length × width" in mm. A common adult measurement might be 190 mm × 85 mm.

### The 60% rule (Rocket Jump Ninja baseline)

The most widely used heuristic:

```
Ideal mouse length ≈ hand length × 0.60
```

Empirically validated across thousands of user reports, internally consistent with
professional setups, and used as a starting point by major reviewers. It is not
peer-reviewed and it oversimplifies grip differences, but it gets you in the right zone.

### Grip-adjusted coefficients

The 60% rule is the average. Different grips need different coefficients because they
distribute hand contact differently.

| Grip style | Length coefficient | Why |
|---|---|---|
| Full Palm | 0.65 to 0.70 | Carpal base needs rear support, undersizing causes instability |
| Relaxed Palm | 0.62 to 0.68 | Moderate rear contact, slight mobility |
| Hover Palm/Claw Hybrid | 0.60 to 0.64 | Pro standard, balance of reference and mobility |
| Claw | 0.58 to 0.64 | Arched fingers, palm barely contacts |
| Fingertip | 0.50 to 0.60 | Minimal contact, smaller mouse allows vertical range |

Working example. Hand length 190 mm:

- Full Palm gripper: 124 to 133 mm mouse (e.g., DeathAdder shape, EC2-DW)
- Hover Palm/Claw Hybrid: 114 to 122 mm mouse (e.g., G Pro X Superlight 2, VAXEE OUTSET AX)
- Aggressive Claw or Fingertip: 95 to 114 mm mouse (e.g., Pulsar X2 Mini, FK2)

Same hand. Three different correct answers depending on grip.

### Width is often more critical than length

A common error is choosing mouse length carefully and ignoring grip width. The constraint
on width is tighter than on length because the wrist cannot compensate for a too-narrow
mouse the way the hand can adapt to a slightly long one.

The Attack Shark / ergonomics community recommendation:

```
Ideal grip width = hand width × 0.55 to 0.65
```

The failure modes are specific and worse than length errors:

**Width too narrow (mouse too thin):** Pinky and ring finger get forced into abduction,
splaying outward to grip the right side. Pinky lock develops after 30 to 60 minutes.
Thenar eminence (thumb base muscle) compresses. Cramping is the symptom.

**Width too wide (mouse too thick):** Fingers spread apart unnaturally to wrap the shell.
Lateral hand muscles fatigue. Micro-precision drops because the hand position is wider
than the natural neutral grip.

Both errors accumulate over months. Width oversizing (too narrow mouse) is the more
common pitfall because manufacturers default to slim shapes for the lightweight market.

### The 1HP ratio approach (more precise)

Matthew Hwu's clinical methodology uses height and width ratios rather than length:

| Ratio | Acceptable range | Ideal |
|---|---|---|
| Hand height ÷ mouse height | 1.8 to 2.15 | 2.0 |
| Hand width ÷ mouse width | 1.2 to 1.6 | 1.4 |

This is more sophisticated than the 60% rule because it accounts for hand proportion
variations: not everyone with a 190 mm hand has the same palm depth or width-to-length
ratio. It is also more measurement work. For most players, the 60% rule plus the width
check is sufficient.

### Hand size categories

Where you fall determines which mice are even in your range:

| Category | Hand length | Typical mouse length (hybrid grip) |
|---|---|---|
| Small | under 170 mm | 102 to 110 mm |
| Small to medium | 170 to 180 mm | 105 to 115 mm |
| Medium | 180 to 195 mm | 110 to 122 mm |
| Medium to large | 195 to 205 mm | 117 to 130 mm |
| Large | 205 to 215 mm | 123 to 138 mm |
| Extra large | over 215 mm | over 130 mm |

Most current CS2 pros fall in the medium to medium-large range based on the mice they
use, though hand-size data is not publicly documented for pros and these are inferences,
not measurements.

### When grip cannot compensate for size

A common belief is that grip technique can absorb a poorly fitted mouse. The adaptation
ceiling is roughly 10 to 15 percent mismatch. Beyond that, you are forcing biomechanical
misalignment, not adapting.

Specific cases where grip cannot save the fit:

- 165 mm hand on a 130 mm palm-grip mouse: fingers cannot reach buttons without high tension
- 210 mm hand on a 115 mm claw mouse: palm has no rear contact, no reference point
- Wide hand on a narrow shell: pinky lock develops regardless of how light the grip pressure

The week 4 test from earlier applies here too. If you are still fighting the fit after a
month, the fit is wrong. A different grip is not the fix.

### Mouse weight by hand size

There is no scientific formula, but the competitive convention is consistent:

| Hand size | Typical weight range |
|---|---|
| Small (under 17 cm) | 40 to 55 g preferred |
| Medium (17 to 19 cm) | 50 to 70 g (current pro standard) |
| Large (over 19 cm) | 60 to 80 g viable |

The logic is mechanical. A 70 g mouse is proportionally heavier to a small hand than to
a large one because the small hand has less leverage and smaller muscles. For the
pro-standard 50 to 70 g range used today (G Pro X Superlight 2 at 63 g, VAXEE OUTSET AX
at 55 g, Razer Viper V4 Pro at 59 g) the differences within the range matter less than
the fit. A well-fitting 70 g mouse beats a poorly fitting 50 g mouse.

NiKo's DeathAdder V4 Pro at around 88 g is heavier than the modern pro standard. That
works for him because he runs an ergonomic shape with full palm grip at extreme low sens.
The combination tolerates more weight. It would not work at high sens with a hover grip.

---

## Anchoring techniques

A hovered hand without an anchor produces inconsistent input. Modern competitive grip practice
uses multiple anchor points to maintain spatial reference without locking the hand down.

### Pinky scratch

Documented in modern aim coaching: the pinky stays in light, dragging contact with the pad
during movement. Not pressing, just enough that you can feel where it is. This gives the
brain continuous tactile feedback about hand position in space.

The test: during a fast horizontal swipe, your pinky should be able to lightly scratch the
pad surface without lifting. If it's lifting, your palm is too high.

Function: corrects for vertical drift (the most common Hover Palm error) and provides
spatial reference during large movements.

### Palm scratch

Less talked about but equivalent on the opposite side: light contact between the rear of
the palm and the back of the mouse shell. Same principle: sustained tactile reference
without locking down.

### The two-anchor system

Combined, the pinky on the pad and the palm at the rear of the mouse form a triangle of
contact with the fingers on the buttons. That triangle stabilizes the hand without
restricting movement. It is one structural reason Hover Palm/Claw works for both precision
and mobility where pure Claw or pure Fingertip is forced to trade one for the other.

---

## Lift-off mechanics

At pro CS2 sensitivities, the mouse leaves the pad many times per round. Lifting and
re-placing is not a special case. It is part of the normal motion vocabulary, and how
you do it depends on your grip.

### Why low-sens players lift constantly

At the median pro CS2 eDPI (around 880), a 180 degree turn requires about 20 cm of pad
movement. A standard 400 mm mousepad has roughly 35 to 38 cm of usable width after
accounting for mouse footprint. After one and a half full turns, you have run out of pad.
You lift, reposition toward the pad center, and place.

That happens multiple times per round in active play: after each peek, after each
counter-strafe, after each crosshair re-center. For NiKo at 560 eDPI (over 30 cm per 360
degrees), a single full turn nearly exhausts the pad. Lift-offs become near-continuous.
For donk at 1000 eDPI, lift-offs are rarer but still frequent on long swipes.

This is daily mechanical reality, not theory.

### Lift-Off Distance (LOD)

LOD is the height above the pad at which the sensor stops tracking. Below LOD the sensor
sees motion. Above LOD the cursor freezes.

If LOD is too high, lifting the mouse at a slight tilt causes phantom cursor drift before
the sensor fully cuts out. The crosshair walks during a lift that should have been silent.
If LOD is too low, micro-lifts during play (the mouse briefly losing pad contact during
a fast swipe on a textured surface) cut tracking when you didn't intend it.

Modern flagship mice expose LOD as a software setting. Typical options:
- Low: 1 mm (preferred by most pros, minimizes drift on lift)
- Medium: 1.5 to 2 mm (default for most mice)
- High: 2 mm or more (rarely useful for FPS)

The low setting is the pro standard because the precision floor matters more than the
risk of accidental drop-out. Pros prefer that a tiny mid-swipe lift cuts tracking cleanly
over the alternative of phantom drift on intentional lifts.

### The two lift mechanics

There are two ways to lift the mouse, mapped onto two grip styles.

**The scoop (palm grip):** The whole hand lifts as a unit. The palm pulls the mouse up
with it. Heel of the hand and fingers leave the pad at the same time. The forearm assists
the motion. Works well for ergonomic mice with deep contoured shells because the shell
fits the palm and stays in place when lifted.

**The pinch (claw, hover, fingertip):** The fingers grip the mouse and lift it. Thumb
plus pinky pinch the sides, index and middle fingers pull up at the front. Palm contact
breaks first if there was any. This is more controlled than the scoop because it isolates
the lift action to the fingers, but it requires a mouse that supports lateral pinch
grip (symmetrical or low-hump shapes).

The pinch dominates in pro CS2 because pro grips dominate at Hover Palm/Claw. The fingers
already do most of the work in those grips. Lifting is an extension of the existing
mechanic.

### Mouse weight balance and lift behavior

Weight balance dictates how the mouse behaves during lift. Three categories:

**Balanced:** Weight distributed evenly along the length. Lifts cleanly with no tilt
regardless of pinch point. The norm for symmetrical pro mice like the G Pro X Superlight 2
and VAXEE OUTSET AX.

**Slightly rear-heavy:** Weight biased toward the back. Lifts with a slight nose-up
tilt unless you compensate by pinching closer to the rear. Predictable once you adapt.

**Front-heavy:** Weight biased toward the front. The mouse tilts nose-down on lift
unless you compensate. This is the documented problem with the Logitech G Pro X2
SUPERSTRIKE: ProSettings and TechRadar both noted that the HITS click mechanism adds
weight to the front, and grabbing the mouse in the middle causes it to tilt forward.

For lift-heavy play (low sens, movement-heavy maps, KZ or surf), front-heavy is
specifically the wrong characteristic. The tilt forces compensatory grip pressure to
hold the nose down during lift, which is exactly the tension you spent the rest of your
grip technique trying to eliminate. A balanced or slightly rear-heavy mouse is preferable
for any grip that lifts often.

### Re-centering technique

A lift consists of three phases: detach, reposition, place. Each phase has a failure mode.

**Detach:** The mouse must cleanly leave the pad without lateral drift. If you are
dragging at the moment of lift instead of pinching upward, the cursor moves during
detachment. The fix is to fully relax forward pressure before the lift starts.

**Reposition:** While in the air, the hand moves back toward the pad center. The mouse
position relative to the hand should not change during this phase. If you re-grip
in the air, you have lost the reference and the next placement will be at a different
hand position than the previous one. The placement angle and crosshair direction will
drift over a session.

**Place:** The mouse touches down with no horizontal motion. A drag-on-landing is a
common error, especially when transitioning out of a fast swipe. The cursor jumps as
the sensor catches the landing motion. The fix is to come down vertically, not at an
angle.

The full sequence in good technique runs in about 100 to 150 ms. Pros chain multiple
re-centers per round without breaking aim flow because the sequence is automated. For
players developing the mechanic, slowing it down to 300 to 400 ms during practice
isolates the failure points.

### Common lift-off errors

- **Dragging through the lift:** Forward pressure not released, cursor moves on detach
- **Re-gripping mid-air:** Reference position changes between lifts, crosshair drifts over time
- **Tilting on lift:** Front-heavy or rear-heavy mouse not compensated for, the cursor moves vertically as the sensor sees angled motion before fully clearing
- **Slamming down:** Mouse placed with horizontal velocity, cursor jumps at touchdown
- **Lifting too high:** LOD cuts cleanly but the hand loses spatial reference for the next placement
- **Lifting too low on a high-LOD mouse:** Sensor still tracks, lift becomes a swipe

The diagnostic for whether your lifts are clean: aim at a static target in a practice
range, do a deliberate lift and replace, and check whether the crosshair is still on the
target. If it has moved, you have a lift-off problem, and grip alone will not fix it
until the mechanic is clean.

---

## Grip pressure

Across all current pro grips, the consensus is the same: **loose, not tight.**

A tight grip:
- Transmits more finger tremor into the mouse
- Reduces blood flow and accelerates fatigue
- Makes the hand worse at fine motor control over time

The reasoning is physiological. The muscles that grip are largely the same muscles that
fine-tune fingertip pressure on M1. Pre-tensioning them with a tight grip costs you
the precision you need for individual click timing.

The intermediate level (what most pros actually run) is **just enough to keep the mouse
from sliding out of position during a swipe, no more.** Your fingertips should be able to
press M1 individually without involving the rest of the hand. If pressing M1 makes any
other part of your hand tense up, your grip is too tight.

### One practical exception

During spray control on the 11th to 30th bullet of an AK or M4, your hand is countering recoil
across a horizontal arc. Light grip is still correct, but most pros report a transition:
firm fingertip pressure on the first burst (1-10 bullets), then relaxed palm-anchored swipe
for the sustained spray. The transition matters more than the absolute pressure level.

---

## How to find your grip

This is the diagnostic flow, not a recipe.

### Step 1: Identify what you actually need

What do you play most?

- **Mostly held angles, AWPing, defensive positions** → Static micro-aim matters more. Palm-leaning works.
- **Mostly aggressive entries, retakes, T-side push** → Dynamic centering matters more. Hover-leaning works.
- **CS2 + KZ + Surf** → Movement-heavy. Hover-leaning is the better fit.
- **CS2 only, low-sens (≥800 eDPI), patient style** → Standard Palm with forearm anchor (NiKo model) is viable.

### Step 2: Check your current grip against your current mouse

Look at the mouse shape table above. Are you running a grip that fits your hardware,
or are you fighting the shape?

Common mismatches:
- Hover-Palm attempt on ergonomic (right-handed) mouse: the hump kills it
- Full Palm on a low-hump symmetrical mouse: no rear support
- Claw on a long, heavy ergonomic mouse: fingers can't actually arch comfortably

### Step 3: Watch for the tension signals

You're using the wrong grip for your mouse if:
- You feel tension in the back of the hand, wrist, or thumb base after 20 minutes of play
- You catch yourself re-gripping mid-round
- You can't sustain a session longer than 2-3 hours without forearm fatigue
- Your hand position drifts visibly over time (the grip you started with isn't the one you have an hour later)

These are RSI precursors. They don't go away with practice. They accumulate. Combined
with high-volume clicking and inadequate recovery, sustained grip-shape mismatch is a
documented pathway to grip-related tendinopathy.

### Step 4: Try the alternative for at least two weeks

Switching grip styles feels worse for 1-2 weeks. That's normal. Your muscle memory has
calibrated to the old style. The decision point isn't day one. It's around week 3, when
the new grip's costs (initial micro-jitter for Hover; reduced movement smoothness for Palm)
either resolve through training or persist.

If they persist past week 4, you've identified that the alternative isn't right for you.
If they resolve, you've found your grip.

---

## What the grip doesn't fix

Higher mouse precision does not fix:
- Crosshair placement
- Pre-aim habits
- Reaction discipline
- Spray transfer practice
- Map and timing knowledge

These dominate aim outcomes by an order of magnitude over grip refinement. Grip matters,
but it's a foundation, not a multiplier. Get it right once, fitted to your hardware,
your sensitivity, your style, and then stop thinking about it.

The compendium's other sections cover the things that move the needle after the foundation
is solid.

---

## Sources

**Grip biomechanics and pro analysis**
- Hwu, M., *"The Best Mouse Grip Of All Time"*, 1HP, May 16 2024: [1-hp.org](https://1-hp.org/blog/uncategorized/the-best-mouse-grip-of-all-time-highest-accuracy-control-for-aiming/)
- Hwu, M., *"Donk's Grip in Just 5 Minutes"*, 1HP YouTube, August 29 2024: [youtube.com/watch?v=XhzvL-hzdas](https://www.youtube.com/watch?v=XhzvL-hzdas)
- 1HP, esports physiotherapy and ergonomics consultancy: [1-hp.org](https://1-hp.org/)

**Pro player data**
- ProSettings.net: Pro player mouse and sensitivity data (897 tracked CS2 professionals, May 2026)
- specs.gg: Current hardware tracking
- Liquipedia Counter-Strike: player settings reference

**Hand size and mouse sizing**
- Rocket Jump Ninja: mouse size guide and the 60% rule baseline: [rocketjumpninja.com](https://www.rocketjumpninja.com/)
- 1HP: ergonomic mouse fit calculator (hand height and width ratios)
- Attack Shark: 60% rule documentation, hand width and grip width recommendations
- Logitech, ZOWIE, Pulsar: official manufacturer hand-size guidance
- ISO 7250-1:2017: Basic Human Body Measurements for Technological Design
- *"A Design Contribution to Ergonomic PC Mice Development"*, PMC/NCBI peer-reviewed research

**Lift-off and sensor mechanics**
- Sensor datasheets and Lift-Off Distance specifications from current flagship mice (Razer Synapse, Logitech G Hub, ZOWIE documentation)
- ProSettings.net and TechRadar reviews on the Logitech G Pro X2 SUPERSTRIKE weight balance

**Aim mechanics and sensitivity**
- Voltaic aim benchmarks and sensitivity recommendations: [voltaic.gg](https://voltaic.gg/)
- KovaaK's scenario library and aim-trainer documentation
- eDPI calculators for cm/360 conversion
- *"Quantifying Wrist-Aiming Habits with A Dual-Sensor Mouse"*, ACM CHI Conference 2024

**Hardware specifications**
- ZOWIE FK2-DW Wireless: official product page documenting low-hump claw/fingertip design
- Logitech: HITS (Haptic Inductive Trigger System) technical specifications

---

← [Back to Peripherals](README.md) | ← [Back to compendium](../README.md)
