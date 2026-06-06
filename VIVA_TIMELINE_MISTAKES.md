# Timeline Breakdown & Common Mistakes
## Virtual Ambulance Emergency Response Simulation

---

## SCENE-BY-SCENE BREAKDOWN (Memorize This For Discussion)

### SCENARIO 1: AMBULANCE ARRIVAL & ASSESSMENT (0–35 seconds)

#### Beat 1: Establishing Shot (0–6s, frames 0–144)
- **Camera:** CAM_A_ESTABLISH (28mm wide angle)
- **Animation:** 
  - Ambulance: stationary (waiting 0–4s, then enter from +X direction 4–6s)
  - Lead paramedic: idle in ambulance for 6s
  - Support paramedic: idle in ambulance for 6s
  - Patient: laying on ground, stationary
- **Lighting:** All street lights on (warm), no flashers yet
- **Audio cue:** Scene ambience starts, distant siren begins

#### Beat 2: Ambulance Arrival (6–12s, frames 144–288)
- **Camera:** CAM_B_ARRIVAL (35mm, 3/4 front angle)
- **Animation:**
  - Ambulance: decelerates, stops near patient (6–12s), red/blue flashers activate
  - Lead paramedic: exits vehicle, runs toward patient
  - Support paramedic: exits vehicle, runs toward patient
  - Patient: still laying, no animation
- **Lighting:** Flashers activate (RED 800W ↔ 20W every 6f, BLUE offset by 6f)
- **Audio:** Ambulance siren reaches peak, paramedic footsteps

#### Beat 3: Approach & Kneel (12–26s, frames 288–624)
- **Camera:** CAM_C_RUN (50mm, side medium) + CAM_D_KNEEL (50mm, over-shoulder)
- **Animation:**
  - Lead paramedic: Run (0–6s), kneel down (6–18s), assess patient (18–26s, holding kneel pose)
  - Support paramedic: Run (0–6s), stands back, watches
  - Patient: laying, no animation
- **Lighting:** Flashers continue, headlights sweep across
- **Audio:** Paramedic voice communication begins

#### Beat 4: Close-up Assessment (26–35s, frames 624–840)
- **Camera:** CAM_E_ASSESS_CU (85mm, close-up, shallow DOF)
- **Animation:**
  - Lead paramedic: holding assessment pose (checking vital signs, gesturing with hands)
  - Support paramedic: standing nearby
  - Patient: laying passively
- **Lighting:** Patient zone rim light (40W) activates for readability
- **Audio:** Medical assessment dialogue

**S1 Summary:** 35s total · paramedics assess patient · establish emergency lighting + urgency

---

### SCENARIO 2: PATIENT EXTRACTION & LOADING (35–78 seconds, frames 840–1872)

#### Beat 5: Stretcher Preparation (35–44s, frames 840–1056)
- **Camera:** CAM_F_S2WIDE (35mm, wide shot)
- **Animation:**
  - Support paramedic: runs to ambulance, retrieves stretcher, returns to patient area
  - Lead paramedic: continues assessing
  - Stretcher: moves from ambulance to patient zone (carried by support)
  - Patient: laying, still passive
- **Lighting:** All lights continue (flashers + headlights + street)
- **Audio:** Stretcher wheels, footsteps, radio chatter

#### Beat 6: Patient Lift (44–54s, frames 1056–1296)
- **Camera:** CAM_G_LIFT (50mm, side medium, shows both paramedics)
- **Animation:**
  - Lead paramedic: uses carry animation (0–41f window)
  - Support paramedic: uses carry animation (0–41f window), synchronized
  - Patient: transitioning from lay → on stretcher (upright)
  - Stretcher: patient lifted onto it
- **Lighting:** Steady lighting, flashers continue
- **Audio:** Exertion sounds, instructions ("on three", etc.)

#### Beat 7: Carry to Ambulance (54–66s, frames 1296–1584)
- **Camera:** CAM_H_CARRY_TRACK (40mm, animated tracking camera)
  - Camera moves X: 3m → 9m (tracking alongside paramedics)
  - Follows stretcher motion from patient zone to ambulance
- **Animation:**
  - Lead paramedic: carry animation (41–117f window), walking forward
  - Support paramedic: carry animation (41–117f window), walking forward
  - Stretcher: horizontal translation toward ambulance
  - Patient: on stretcher, elevated
- **Lighting:** Headlights illuminate path
- **Audio:** Footsteps on pavement, steady breathing

#### Beat 8: Loading Into Ambulance (66–74s, frames 1584–1776)
- **Camera:** CAM_I_LOAD (50mm, rear ambulance view)
- **Animation:**
  - Lead paramedic: carry animation (117–138f window), loading patient through rear doors
  - Support paramedic: steady stretcher
  - Patient: being loaded, horizontal → climbing into ambulance
  - Stretcher: loading into ambulance cargo bay (Z increases)
- **Lighting:** Rear ambulance lights illuminate loading area
- **Audio:** Stretcher securing sounds, door mechanisms

#### Beat 9: Ambulance Departure (74–78s, frames 1776–1872)
- **Camera:** CAM_J_DEPART (85mm, telephoto, long lens compression)
- **Animation:**
  - Ambulance: starts engine, reverses slowly out of frame
  - Paramedics: inside ambulance (not visible)
  - Lights: flashers continue, siren fades
- **Lighting:** Flashers steady (no longer alternating — stopped sequence)
- **Audio:** Siren volume decreases, ambulance engine + transmission, fade to black

**S2 Summary:** 43s total · extract patient → load into ambulance → depart scene

---

## COMMON MISTAKES (Prepare To Defend Against These)

### Animation Mistakes

**Mistake 1: Root Motion Drift**
- **What:** Character teleports between animation clips
- **Cause:** Root bone has location offsets across clips. NLA evaluates them cumulatively.
- **Red flag:** "Your characters jump around the scene"
- **Defense:** "We normalized all root XY offsets by subtracting start value from all keyframes on import. This ensures characters remain at their NLA location keyframes."
- **Code example:** Subtract `fcurve.keyframe_points[0].co[1]` from all points

**Mistake 2: Foot Sliding**
- **What:** Feet appear to glide across ground during movement
- **Cause:** Root motion speed ≠ foot step frequency. Local foot animation mismatches world translation.
- **Red flag:** "The character slides like they're on ice"
- **Defense:** "We use in-place animation with scaled NLA strips, and we matched step timing to root motion. An improvement would be IK foot constraints."
- **Formula:** $v_{foot,world} = v_{root} + R_{arm} × v_{foot,local}$ must = 0 during contact

**Mistake 3: Animation Jank At Transitions**
- **What:** Pose snaps or jerks when clips transition
- **Cause:** No blending between strips (blend_in/blend_out = 0)
- **Red flag:** "Animation looks choppy at beat changes"
- **Defense:** "We set blend_in/blend_out = 8–12 frames, creating a 0.33–0.5s smooth transition. Without blending, pose changes are instant."

**Mistake 4: Wrong Animation For Context**
- **What:** Character performs nonsensical animation (e.g., running while carrying patient)
- **Cause:** Mixamo clips don't have all variations. Used approximation (run for walk, carry for transport)
- **Red flag:** "Your stretcher carrying looks unnatural"
- **Defense:** "Mixamo doesn't provide a 'walk while carrying' animation. We used the carry animation which has slow forward motion. With more time, we would import a dedicated walk clip."

---

### Lighting Mistakes

**Mistake 5: Night Scene Too Dark**
- **What:** Character details invisible, looks like silhouettes
- **Cause:** Insufficient fill light; relying on single hard key light
- **Red flag:** "I can't see what's happening"
- **Defense:** "We implemented three-layer lighting: key (headlights, 1200W), fill (moon, 0.08W minimum), rim (patient zone, 40W). Plus Eevee exposure +0.4 and Filmic tone mapping which preserves shadow detail."

**Mistake 6: Unrealistic Light Falloff**
- **What:** Lights at 50m still bright; or nearby lights barely visible
- **Cause:** Forgot inverse square law. Hard-coded intensity instead of calculating for distance.
- **Red flag:** "Your lighting makes no physical sense"
- **Defense:** "We applied inverse square law `E = P/(4πr²)` for all point lights. Street lights at 180W are calibrated for ~5m radius. At 10m, they're 4× dimmer."

**Mistake 7: Flashers Look Fake**
- **What:** Emergency lights blink harshly; no glow or realism
- **Cause:** Animated energy on/off (800W→0W). No bloom. Wrong color.
- **Red flag:** "Emergency lights look cartoonish"
- **Defense:** "We animated energy 800W↔20W (never fully off) every 6 frames (2Hz strobe). With Eevee bloom (threshold 0.8, radius 6.5, intensity 0.25), flashers create realistic glow around bright pixels."

---

### Technical Mistakes

**Mistake 8: Scale Not Applied**
- **What:** Characters appear half-size or objects misaligned with their visual size
- **Cause:** Set scale but didn't call `transform_apply`
- **Red flag:** "Objects look wrong relative to each other; Armature doesn't match mesh size"
- **Defense:** "SKILL.md Rule 3: we call `transform_apply(scale=True)` immediately after every scale assignment, inside loops. This bakes scale into mesh vertex data."

**Mistake 9: Animations Don't Import**
- **What:** Error on GLB import; animations not readable
- **Cause:** ASCII FBX files; Blender 5.1 incompatibility
- **Red flag:** "I couldn't get your animations working"
- **Defense:** "Mixamo exports ASCII FBX; Blender 5.1 only supports binary FBX/GLB. We converted with FBX2glTF to binary GLB, which imports directly with skeleton + weights + animations preserved."

**Mistake 10: NLA Strips Misaligned**
- **What:** Animations play at wrong times; characters freeze between clips
- **Cause:** Strip frame ranges don't align; no extrapolation set
- **Red flag:** "Animation has gaps; characters hold weird poses"
- **Defense:** "We set each NLA strip frame range precisely (e.g., run [288–432], kneel [432–840]) with no gaps. We set `extrapolation='HOLD_FORWARD'` so strips hold their final frame after ending, preventing pose snap."

---

### Conceptual Mistakes

**Mistake 11: Confusing Material & Texture**
- **What:** Saying "We added texture to the ambulance" when you meant "material"
- **Cause:** Imprecise terminology
- **Red flag:** Professor: "Are you saying you imported texture images?"
- **Defense:** "Clarification: the ambulance uses an AMB_White material (Principled BSDF with solid base color 0.92,0.92,0.92, no image texture). We didn't apply texture maps due to time constraints."

**Mistake 12: Confusing Eevee Approximations With Physical Correctness**
- **What:** Claiming Eevee produces physically accurate renders
- **Cause:** Misunderstanding renderer capabilities
- **Red flag:** "Your scene is physically correct"
- **Defense:** "Eevee approximates physical results using real-time techniques (shadow maps, SSAO, baked probes). It's not physically accurate; Cycles path tracing is. We chose Eevee for speed."

**Mistake 13: Incorrect Inverse Square Law**
- **What:** Saying "light falls off exponentially" or "light halves every meter"
- **Cause:** Confusion with other formulas
- **Red flag:** Professor asks "How does light intensity change with distance?"
- **Defense:** "`E = P/(4πr²)` — inverse square law. Doubling distance quadruples the sphere area, so illuminance quarters (falls off as 1/r², not 1/r or exponential)."

---

## BEFORE THE VIVA: FINAL CHECKLIST

- [ ] Memorized project facts (78s, 1872 frames, 271 objects, 13 lights, 3 armatures)
- [ ] Can draw the scene hierarchy from memory (collections, objects, armatures)
- [ ] Can explain why each software choice (Blender, Mixamo, Eevee, FBX→GLB conversion)
- [ ] Can describe all 10 cameras and their focal lengths
- [ ] Can explain NLA strip scale + blend_in/blend_out math
- [ ] Can state inverse square law + Fresnel + rendering equation from memory
- [ ] Can name all 5 animation clips and their frame counts
- [ ] Can explain SKILL.md rules 1, 3, 4 with examples
- [ ] Can describe three-point lighting setup in your scene
- [ ] Can explain what would cause foot sliding and how to fix it
- [ ] Can defend each creative/technical choice if challenged
- [ ] Can acknowledge limitations (missing IK, walk animation, departure anim, cloth sim, particles)

---

*Print this page. When professor challenges your work, reference the defense statements.*
