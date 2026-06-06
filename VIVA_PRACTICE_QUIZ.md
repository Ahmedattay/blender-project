# Practice Quiz — Test Your Knowledge
## Virtual Ambulance Emergency Response Simulation

---

## PART A: MULTIPLE CHOICE (Easy)

**A1. What is the total duration of your animation?**
- A) 60 seconds
- B) 78 seconds ✓
- C) 90 seconds
- D) 120 seconds

**A2. How many bones does the Mixamo humanoid skeleton have?**
- A) 32
- B) 47
- C) 65 ✓
- D) 128

**A3. Which render engine did you use and why?**
- A) Cycles (physically accurate)
- B) Eevee (fast rasterization) ✓
- C) LuxRender (film quality)
- D) Arnold (VFX standard)
*Answer: Eevee for ~30min render vs ~30hr with Cycles*

**A4. What file format did you convert Mixamo FBX files to?**
- A) GLTF (ASCII)
- B) GLB (binary) ✓
- C) USD (Pixar)
- D) FBX (binary)

**A5. How many lights are in your scene?**
- A) 7
- B) 10
- C) 13 ✓
- D) 20

**A6. What is the root bone of the Mixamo skeleton?**
- A) mixamorig:Spine
- B) mixamorig:Hips ✓
- C) mixamorig:LeftFoot
- D) mixamorig:Armature

**A7. What does HOLD_FORWARD do on an NLA strip?**
- A) Loops the animation
- B) Holds final frame after strip ends ✓
- C) Reverses playback
- D) Scales animation duration

**A8. What is the inverse square law formula?**
- A) $E = P × r²$
- B) $E = P / r$ 
- C) $E = P / (4πr²)$ ✓
- D) $E = P × (4π/r)$

**A9. What is metallic=0.5 in Principled BSDF?**
- A) Scientifically correct
- B) Physically implausible blend ✓
- C) Perfect for materials
- D) Unused parameter

**A10. Which project scenario covers patient extraction?**
- A) Scenario 1 (0–35s)
- B) Scenario 2 (35–78s) ✓
- C) Both scenarios
- D) Post-credits

---

## PART B: MULTIPLE CHOICE (Medium)

**B1. Why did you convert ASCII FBX → GLB?**
- A) GLB is faster to load
- B) Blender 5.1 dropped ASCII FBX import support ✓
- C) GLB is smaller file size
- D) Better animation compatibility

**B2. What is Linear Blend Skinning?**
- A) Blending between two skeletal meshes
- B) $v = Σ(w_i × M_i × v_{bind})$ ✓ (weighted bone matrices)
- C) Linear interpolation of keyframes
- D) Blending NLA tracks

**B3. How does NLA strip scale affect timing?**
- A) No effect — scale is cosmetic
- B) scale = strip_duration / action_duration ✓
- C) scale directly sets animation speed
- D) scale multiplies frame number

**B4. What causes foot sliding?**
- A) Incorrect weight painting
- B) Root motion not matching step velocity ✓
- C) Camera angle too steep
- D) Missing footstep sounds

**B5. What is the rendering equation?**
- A) $I = k × θ$
- B) $L_o = L_e + ∫ f_r L_i \cos θ dω$ ✓
- C) $E = P / r²$
- D) $v = u + at$

**B6. What is Filmic tone mapping?**
- A) Linear gamma correction
- B) Photographic S-curve preserving shadows ✓
- C) Simple clamp to 0–1
- D) Exposure adjustment only

**B7. Why use area lights for emergency flashers vs point lights?**
- A) Point lights are harder to configure
- B) Area lights are cheaper computationally
- C) Area lights produce realistic soft shadows ✓
- D) Point lights don't work at night

**B8. What is the maximum weight sum per vertex?**
- A) 0.5
- B) 0.8
- C) 1.0 ✓
- D) 1.5

**B9. What is volumetric density = 0.008 used for?**
- A) Opaque fog
- B) Light atmospheric haze ✓
- C) Smoke particles
- D) Fire simulation

**B10. What does `transform_apply` do?**
- A) Applies transforms to keyframes
- B) Bakes transforms into mesh vertex data ✓
- C) Applies modifiers to the mesh
- D) Applies materials to objects

---

## PART C: SHORT ANSWER (Medium)

**C1. Explain SKILL.md Rule 3 and why it matters.**

*Expected:* Call `bpy.ops.object.transform_apply(scale=True)` immediately after setting scale, inside loop. Delay causes `bpy.context.active_object` to reference wrong object. Physics/Armature/normals computed in object space — mismatched scales cause misalignment.

**C2. Write the diffuse lighting equation and explain why we use `max(0, N·L)`.**

*Expected:* $I_d = k_d × I_L × \max(0, N·L)$. The `max` prevents negative values when light is behind surface (θ > 90°). Physically correct — surfaces never illuminate from behind.

**C3. Describe the NLA strip evaluation formula and calculate action_frame for scene frame 350 in our run strip.**

*Expected:* $action\_frame = action\_start + (f - strip\_start) × (1/scale)$. For run strip [288–432], scale=2.67, action_start=0: action_frame = 0 + (350–288) × (1/2.67) ≈ 23.2 frames.

**C4. What is the difference between root motion and in-place animation? Why did we choose in-place?**

*Expected:* Root motion: Hips carries world translation (character moves automatically). In-place: Hips stays near origin, must keyframe location manually. We used in-place + zeroed root XY offsets to prevent drift between clip boundaries.

**C5. Explain the Fresnel effect using Schlick's approximation.**

*Expected:* $F(θ) = F_0 + (1-F_0)(1-\cos θ)^5$. Reflection increases at grazing angles. At θ=0° (normal): F=$F_0$ (2–8%). At θ=90° (grazing): F=100%. In our scene: wet road reflects emergency lights strongly at grazing angles.

**C6. What is the Cook-Torrance BSDF? Name the three components.**

*Expected:* $f_r = (D × F × G) / (4 × (N·L) × (N·V))$. D=GGX distribution (microfacet orientation). F=Fresnel (reflection fraction). G=Smith geometry (shadowing-masking). Used in Principled BSDF.

**C7. Explain the Blender 5.1 Action API change from Blender 4.x and how it breaks old animation code.**

*Expected:* 4.x: `Action.fcurves` flat list. 5.1: Layered — `Action.layers[0].strips[0].channelbags[0].fcurves`. Old code accessing `Action.fcurves` fails because the property is removed.

**C8. Why is transform_apply necessary after setting object scale?**

*Expected:* Mesh data stores vertices in object space. Scale transforms affect interpretation. Physics/Armature/normals computed in object space — if scale not applied, all calculations misalign. Baking scale into vertex data synchronizes all systems.

**C9. Describe the three-point lighting setup for our night scene.**

*Expected:* Key: ambulance headlights (white SPOT, hard). Fill: moon (blue SUN, 0.08W, prevents total black). Rim: patient zone area light (40W, blue-purple, separates from background). Additional: 7 street lights (motivated sources, 180W each, warm sodium spectrum).

**C10. What is bloom in Eevee and why is it critical for our emergency flashers?**

*Expected:* Bloom: glow effect simulating lens flare around bright sources. Post-process: extract bright pixels (threshold=0.8), blur, add back. Our settings: intensity=0.25, radius=6.5. Critical because it makes 800W flashers look cinematic with distinctive glow.

---

## PART D: LONG ANSWER (Difficult)

**D1. Derive the relationship between focal length, FOV, and sensor width. Show the calculation for our 28mm establishing shot.**

*Expected:*
$$\text{FOV} = 2 × \arctan\left(\frac{\text{sensor\_width}}{2 × \text{focal\_length}}\right)$$

For 35mm full-frame sensor (36mm width), 28mm lens:
$$\text{FOV} = 2 × \arctan(36 / (2 × 28)) = 2 × \arctan(0.643) ≈ 73°$$

This is a wide angle, good for establishing shots showing entire scene.

**D2. Explain why Linear Blend Skinning produces "candy wrapper" artifacts and how Dual Quaternion Skinning fixes it.**

*Expected:* LBS averages rotation matrices: $v = Σ(w_i × M_i × v)$. But linear interpolation of rotation matrices produces non-orthogonal matrices (not valid rotations). At twisting joints, weighted average creates shearing — vertices converge toward bone axis, losing volume ("candy wrapper" effect).

DQS uses quaternion representation: $v = (Σ(w_i × q_i)) / |Σ(w_i × q_i)|$. Interpolates on unit quaternion manifold (always valid rotation), preserves volume.

**D3. Explain the complete render pipeline for one frame in Eevee, from scene evaluation to display.**

*Expected:*
1. **Scene evaluation:** Compute all transforms (F-curves, NLA evaluation, constraints)
2. **Visibility culling:** Determine objects in camera frustum
3. **Shadow passes:** Render depth buffers from each light's POV (1024px cube maps)
4. **Main rendering:** Per-fragment: material shader, direct lighting (shadow map lookup), indirect from probes/SSAO
5. **Volumetric pass:** Render participating media (fog scattering/absorption)
6. **Post-processing:** Bloom (bright-pass → multi-scale blur → add), motion blur (velocity buffer), tone mapping (Filmic curve)
7. **Display:** sRGB gamma encoding for monitor output

**D4. The professor asks: "Your scene looks unrealistic. What would you improve?"**

*Expected:*
1. **IK foot constraints** — eliminate foot sliding, keep feet grounded during locomotion
2. **Walk animation** — import dedicated walk clip for stretcher carry (currently using run, looks wrong)
3. **Texture maps** — add normal maps and roughness maps to ambulance/clothing (currently solid colors)
4. **Particle systems** — debris from accident, ambulance exhaust smoke
5. **Ambulance departure** — currently stops parked; add reverse + turn away animation
6. **Multi-camera edit** — use camera binding markers for proper multi-shot sequence instead of fixed camera
7. **Sound design** — synchronization marks for audio in post-production

---

## PART E: TECHNICAL DEBUGGING (Very Difficult)

**E1. You import a Mixamo run animation into your character, but the character teleports 2 meters forward when the strip plays. What causes this? How do you fix it?**

*Expected:* Root motion in the action — Hips.location has X,Y keyframes that move the character. At animation start, Hips is at (0, 0, z); by end, it's at (2, 0, z). When NLA evaluates the strip, it applies these location changes on top of the character's location keyframes, causing teleport.

**Fix:** Normalize root bone: subtract start position from all Hips.location keyframes so motion is relative to (0, 0).

**Code sketch:**
```python
start_x = action.layers[0].strips[0].channelbags[0].fcurves[0].keyframe_points[0].co[1]
for fcurve in channelbag.fcurves[:2]:  # X, Y channels
    for kf in fcurve.keyframe_points:
        kf.co[1] -= start_x  # subtract start from all values
```

**E2. You have an NLA strip with blend_in=12. At frame 100, the strip is at influence=0.3. What scene frame is this? (Strip runs [100–200], action_frame_start=0)**

*Expected:*
During blend_in (frames 100–112): $\text{influence} = (f - 100) / 12$
If influence = 0.3: $0.3 = (f - 100) / 12$ → $f = 100 + 3.6 = 103.6$

Scene frame **103.6 (or frame 104 rounded)**.

**E3. You set a character's scale to (2, 2, 2) but forget to call transform_apply. Later you add an Armature modifier and the character appears half the size. Why?**

*Expected:* Armature modifier computes deformation in object space: $v = Σ(w_i × M_i × M_{i,rest}^{-1} × v_{object})$.

With scale=(2,2,2) not applied: mesh vertices are at ±1 (not ±2). Armature works on these ±1 vertices → character displays at half-scale.

**Fix:** Call `bpy.ops.object.transform_apply(scale=True)` to bake scale into vertices.

---

## PART F: ESSAY (Very Difficult)

**F1. Write a 300-word explanation of how you solved the FBX import problem in Blender 5.1.**

*Expected:*
- Mixamo exports ASCII FBX (text-based, large files)
- Blender 5.1 dropped ASCII FBX support (security/complexity reasons)
- Solution: Use FBX2glTF (Meta's open-source converter) to convert ASCII FBX → binary GLB
- GLB = Khronos Group's glTF binary format (modern, open standard, optimized for runtime)
- Conversion preserves: mesh geometry, UV maps, vertex weights, skeleton hierarchy, animations
- In Blender: File → Import → glTF 2.0 (.GLB/.gltf) handles binary GLB files natively
- Result: All Mixamo animations (run, kneel, carry, lay, patient_carry) imported successfully with skeleton intact

---

**Answers at bottom of this file for self-checking.**

---

## ANSWER KEY

### Part A
1. B · 2. C · 3. B · 4. B · 5. C · 6. B · 7. B · 8. C · 9. B · 10. B

### Part B
1. B · 2. B · 3. B · 4. B · 5. B · 6. B · 7. C · 8. C · 9. B · 10. B

### Part C–F
See expected answers embedded above.

---

*Score: Each question worth 1 point. Aim for 80%+ on Parts A–B before attempting C–F.*
