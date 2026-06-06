# Viva Prep — Condensed Handbook
## Virtual Ambulance Emergency Response Simulation

---

## PROJECT FACTS (Memorize These)

| Item | Value |
|---|---|
| Duration | 78 seconds · 1,872 frames · 24fps |
| Scenarios | S1: 0–35s (arrival+assessment) · S2: 35–78s (extraction+loading) |
| Characters | 3 armatures: Armature (lead), EMS_Paramedic_Support, Armature.001 (patient) |
| Meshes per char | 7 (body, shirt, pants, shoes, cap, eyelashes, mask) |
| Scene objects | 271 total across 9 collections |
| Lights | 13: 7 street (POINT 180W) · 2 flashers (AREA 800W animated) · 2 headlights (SPOT 1200W) · 1 moon (SUN 0.08W) · 1 rim (AREA 40W) |
| Cameras | 10: 28mm/35mm/50mm/85mm lenses for wide→close coverage |
| Render engine | Blender Eevee · 1920×1080 · TAA 128 samples |
| Source animations | 5 Mixamo FBX → converted to GLB via FBX2glTF |

---

## WHY EACH DECISION WAS MADE

**Blender:** Free, complete pipeline (model→animate→light→render), full Python API (bpy) for automation.

**Mixamo:** Standard 65-bone skeleton, motion-capture quality, free. Saves 40+ hours of manual rigging.

**FBX → GLB conversion:** Mixamo exports ASCII FBX. Blender 5.1 only supports binary FBX/GLB. Used FBX2glTF (Meta open-source) to convert.

**Eevee over Cycles:** Eevee ~1s/frame = ~31min total. Cycles ~60s/frame = ~30 hours. Eevee gives 80% quality at 5% render time.

**NLA over baked animation:** Non-destructive clip composition. Each character has independent tracks for idle/locomotion/task. Editable without re-baking.

**Night lighting choices:** Street lights = warm sodium (realistic HPS spectrum). Flashers = animated 800↔20W red/blue alternating every 6f (2Hz strobe). Headlights = cool white SPOT creating volumetric cones through fog. Moon = dim blue fill to prevent total black shadows.

---

## CORE CG CONCEPTS

### Rendering
| Concept | 1-Line Definition |
|---|---|
| Rasterization | Convert triangles → pixels. Used by Eevee. O(n×pixels). |
| Ray Tracing | Trace rays from camera → scene → compute lighting physically. Used by Cycles. |
| Path Tracing | Monte Carlo integration of rendering equation. Noise ∝ 1/√samples. |
| Rendering Equation | `L_o = L_e + ∫ f_r L_i cosθ dω` — ALL light transport governed by this. |
| Tone Mapping | Convert HDR linear → displayable LDR. Filmic: S-curve, preserves shadows. |
| Shadow Map | Depth buffer from light's POV. Resolution = shadow sharpness. We used 1024px. |
| Bloom | Glow around bright sources (lens scatter). Threshold=0.8, radius=6.5 in our scene. |
| Ambient Occlusion | Approximates how much ambient light a point receives. Concave = darker. |
| Volumetric | Light scattering in fog/smoke. We use density=0.008 for atmospheric haze. |

### Lighting
| Formula | Meaning |
|---|---|
| `I_diffuse = k_d × I_L × max(0, N·L)` | Lambertian (matte) reflection |
| `I_specular = k_s × I_L × max(0, R·V)^n` | Phong (glossy) reflection |
| `E = P / (4πr²)` | Inverse square law — brightness halves every 1.41× distance |
| `F(θ) = F₀ + (1-F₀)(1-cosθ)^5` | Fresnel — more reflection at grazing angles |

**PBR (Principled BSDF):** Base Color + Metallic (0=plastic/1=metal) + Roughness (0=mirror/1=matte). Energy conserving. `f_r = (D×F×G) / (4×(N·L)×(N·V))` — Cook-Torrance microfacet model.

**Three-point lighting in our scene:** Key = headlights (white SPOT, hard) · Fill = moon (blue SUN, 0.08W) · Rim = patient zone (blue-purple AREA, 40W).

### Materials
- **Material:** Complete shading model (node tree → Principled BSDF → Material Output)
- **Texture:** 2D image providing data to material channels. Material ≠ texture.
- **Emission material:** Makes surface glow visually — does NOT illuminate other objects in Eevee. Need a separate light object for that.
- **Normal map:** Per-pixel normal perturbation stored in RGB. Adds detail without geometry. Cheaper than displacement.

---

## ANIMATION SYSTEM

### Hierarchy
```
Armature Object
  └── Bone hierarchy (mixamorig:Hips → Spine → ... → LeftFoot)
        └── Pose (FK rotations per bone)
              └── Stored as F-curves in Actions
                    └── Organized as NLA strips on NLA tracks
```

### Key Definitions
- **Armature:** Hierarchy of bones deforming a mesh via vertex weight groups.
- **Vertex weight:** Influence (0–1) of a bone on a vertex. Weights per vertex must sum to 1.
- **Linear Blend Skinning:** `v_world = Σ(w_i × M_i × v_bind)` — weighted bone transforms.
- **F-Curve:** Property value plotted over time (one curve per channel: location.x, rotation.y, etc.).
- **Action:** Data block containing F-curves = one animation clip.
- **NLA Strip:** References an Action, placed on a timeline with scale/blend settings.
- **NLA Track:** Layer of strips. Tracks stack bottom-up (Replace blending).

### NLA Strip Math
```
action_frame = action_start + (scene_frame - strip_start) × (action_len / strip_len)
influence = (scene_frame - strip_start) / blend_in   [during blend-in window]
```

### Root Motion vs In-Place
- **Root motion:** Hips bone carries world translation → character moves automatically.
- **In-place:** Hips stays near origin → must keyframe armature location manually.
- **Our choice:** In-place + location keyframes. We zeroed all root XY offsets on import.

### FK vs IK
- **FK:** Rotate from root→tip. Natural for spine/gestures. All our Mixamo clips use FK.
- **IK:** Specify end position, joints solved automatically. Needed for foot placement.
- **Missing:** IK foot constraints = listed improvement. Would eliminate foot sliding.

### Foot Sliding Math
`v_foot_world = v_root + R_arm × v_foot_local`. Sliding occurs when this ≠ 0 during contact (foot Z ≈ 0).

### Our Animation Clips
| Action | Source | Length | Used For |
|---|---|---|---|
| paramedic_paramedic_run_act_01 | paramedic_run-with_sckin.fbx | 54f | Walk/run approach, S2 retrieve |
| paramedic_paramedic_kneel_act_01 | paramedic_kneel.fbx | 66f | Kneel + assessment (split at 33f) |
| paramedic_paramedic_carry_act_01 | paramedic_carry.fbx | 138f | Lift (0-41f), carry (41-117f), load (117-138f) |
| patient_patient_laying_act_01 | patient_laying.fbx | 51f | Patient passive (repeat=16.47 for 840f) |
| patient_patient_being_carried_act_01 | patient_being_carried-with_skin.fbx | 330f | Lift/carry/load passive |

---

## MIXAMO SPECIFICS

- **Skeleton:** 65 bones, prefix `mixamorig:`, root = `mixamorig:Hips`
- **No retargeting needed:** All characters use identical skeleton — animations apply directly.
- **With skin vs without:** "With skin" includes mesh geometry + weights. "Without skin" = animation only.
- **ASCII FBX problem:** Blender 5.1 dropped ASCII FBX support. Converted with FBX2glTF.
- **Blender 5.1 Action API:** New layered system — `Action.fcurves` removed. Must use `Action.layers[0].strips[0].channelbags[0].fcurves`.

---

## SCENE STRUCTURE

```
Collections:
  SCN1_Ambulance_Arrival    → AMB_Body, AMB_Cabin, EMS_Paramedic_Support
  SCN2_Patient_Extraction   → STRETCHER_Root, platform, 4 legs
  EMS_Characters
    ├─ Paramedic             → Armature + Ch16_Body1/Cap/Shirt/Pants/Shoes/Eyelashes/Mask
    └─ Patient               → Armature.001 + Prisoner
  EMS_Cameras               → CAM_A through CAM_J (10 cameras)
  LIGHTS_Street             → 7 poles + 7 lamp heads + 7 point lights
  LIGHTS_Ambulance          → AMB_Flash_RED, AMB_Flash_BLUE, AMB_Head_L, AMB_Head_R
  EMS_Blocking              → ANCHOR_*, PATH_* empties (hidden in render)
```

---

## SKILL.md RULES (Examiner Will Ask)

**Rule 1 (size=2):** `primitive_cube_add(size=2)` → vertices at ±1 → `scale=(hx,hy,hz)` = actual half-extents. With `size=1`, scale is halved silently.

**Rule 2 (no cylinder rotation):** Never rotate cylinders to point A→B. Use bmesh with explicit vertex math to avoid Euler rotation axis errors.

**Rule 3 (transform_apply immediately):** Call `bpy.ops.object.transform_apply(scale=True)` right after setting scale, inside the loop. Delay causes `bpy.context.active_object` to reference wrong object.

**Rule 4 (measure neighbors):** Never hardcode dimensions for spanning parts. Use `verify_bounds()` on neighbors and compute from real extents.

---

## TOP 20 DISCUSSION Q&A

**Q: What is an armature?** Hierarchy of bones deforming a mesh via weighted vertex groups.

**Q: What is the NLA editor?** System for compositing multiple animation clips on tracks without baking.

**Q: Why size=2 for cubes?** Vertices at ±1, so scale = actual half-extent. Prevents silent 2× scale error.

**Q: What is root motion?** Hips bone carrying world translation — causes character to move through the scene.

**Q: What causes foot sliding?** World foot velocity ≠ 0 during ground contact phase. Fix: IK constraints.

**Q: What is HOLD_FORWARD?** NLA strip extrapolation — holds final frame after strip ends, prevents pose snap.

**Q: Material vs texture?** Material = shading model. Texture = 2D image data fed into material inputs.

**Q: Eevee vs Cycles?** Eevee: rasterization, ~1s/frame, approximate. Cycles: path tracing, ~60s/frame, physically accurate.

**Q: Why FBX → GLB?** Mixamo exports ASCII FBX. Blender 5.1 dropped ASCII FBX. GLB = open binary format.

**Q: What is PBR?** Energy-conserving shading with physically measured properties (metallic, roughness).

**Q: What is transform_apply?** Bakes object transform (scale/rotation) into mesh vertex data, resets to identity.

**Q: What is the inverse square law?** `E = P/(4πr²)` — light intensity quarters when distance doubles.

**Q: What is the Fresnel effect?** All surfaces become more reflective at grazing angles.

**Q: What is FK vs IK?** FK: rotate bones root→tip. IK: specify end position, joints solved automatically.

**Q: What is LBS?** Linear Blend Skinning: `v = Σ(w_i × M_i × v_bind)` — vertex deformed by weighted bone matrices.

**Q: What is a shadow map?** Depth buffer rendered from light's POV. Used to test if surface is in shadow.

**Q: What is bloom?** Glow around bright lights simulating lens scatter. Critical for emergency flashers.

**Q: What is volumetric rendering?** Rendering fog/smoke where light scatters inside volume. We use density=0.008.

**Q: What is tone mapping?** Converting HDR linear render → displayable LDR. Filmic = photographic S-curve.

**Q: What would you improve?** IK feet, walk animation, texture maps, ambulance departure, cloth simulation.

---

## RENDERING EQUATION (Know This Cold)

$$L_o(x, \omega_o) = L_e(x, \omega_o) + \int_\Omega f_r(x, \omega_i, \omega_o) \, L_i(x, \omega_i) \, (\omega_i \cdot n) \, d\omega_i$$

- $L_o$ = outgoing light (what we see)
- $L_e$ = emitted light (glow materials)
- $f_r$ = BSDF (how surface scatters)
- $L_i$ = incoming light (from all directions)
- $\omega_i \cdot n = \cos\theta$ = Lambert's law

**Eevee approximates this integral. Cycles solves it via Monte Carlo path tracing.**

---

*Revision: 2026-06-06 · Use VIVA_PREPARATION_HANDBOOK.md for full detail on any section.*
