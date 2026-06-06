# Technical Reference (Formulas & Code Snippets)
## Virtual Ambulance Emergency Response Simulation

---

## RENDERING EQUATIONS

### Rendering Equation (Kajiya, 1986)
$$L_o(\mathbf{x}, \omega_o) = L_e(\mathbf{x}, \omega_o) + \int_{\Omega} f_r(\mathbf{x}, \omega_i, \omega_o) L_i(\mathbf{x}, \omega_i) (\mathbf{n} \cdot \omega_i) d\omega_i$$

- $L_o$ = outgoing radiance (light leaving surface)
- $L_e$ = emitted light (only for emissive materials)
- $f_r$ = BRDF (bidirectional reflectance distribution function)
- $L_i$ = incoming radiance from direction $\omega_i$
- $\mathbf{n}$ = surface normal
- $\Omega$ = hemisphere above surface

**Interpretation:** Total light leaving a surface = emitted + integral of reflected light over all incoming directions

---

### Cook-Torrance BRDF
$$f_r(\mathbf{l}, \mathbf{v}) = k_d \frac{c}{\pi} + k_s \frac{DFG}{4(\mathbf{n} \cdot \mathbf{l})(\mathbf{n} \cdot \mathbf{v})}$$

- $k_d$ = diffuse coefficient (0 for metals, 1 for dielectrics)
- $k_s$ = specular coefficient (1 for metals, 0 for dielectrics, typically $k_d + k_s ≈ 1$)
- $D$ = microfacet distribution (GGX)
- $F$ = Fresnel term
- $G$ = geometry/shadowing term (Smith)

**Blender Principled BSDF:** Implements Cook-Torrance for PBR

---

### GGX Distribution
$$D(h) = \frac{\alpha^2}{\pi((\mathbf{n} \cdot \mathbf{h})^2(\alpha^2 - 1) + 1)^2}$$

- $\alpha$ = roughness² (Blender: roughness parameter)
- $\mathbf{h}$ = halfway vector between $\mathbf{l}$ and $\mathbf{v}$
- Result: Higher α (rougher) → broader distribution → wider highlights

---

### Schlick's Fresnel Approximation
$$F(\theta) = F_0 + (1 - F_0)(1 - \cos\theta)^5$$

- $F_0$ = reflectance at normal incidence
  - Dielectrics: 2–8% (0.02–0.08 for non-metallic)
  - Metals: 40–95% (material-dependent)
- $\theta$ = angle between view and surface normal
- At $\theta = 0°$ (normal): $F = F_0$
- At $\theta = 90°$ (grazing): $F ≈ 1.0$

**Physical meaning:** All surfaces reflect more at grazing angles

---

### Lambertian Diffuse
$$I_d = k_d \times I_L \times \max(0, \mathbf{N} \cdot \mathbf{L})$$

- $I_d$ = diffuse intensity
- $k_d$ = diffuse color (albedo, base color)
- $I_L$ = light intensity
- $\mathbf{N} \cdot \mathbf{L} = \cos\theta$ (Lambert's law: cosine law)
- $\max(0, ...)$ = prevents light from behind surface

**Law:** Illuminance on surface ∝ $\cos\theta$ (perpendicular > oblique)

---

### Inverse Square Law
$$E = \frac{P}{4\pi r^2}$$

- $E$ = illuminance (lux, lumens/m²)
- $P$ = power (lumens)
- $r$ = distance from point light source

**Application:** 
- Distance doubled → illuminance becomes 1/4
- Our street lights (180W at ~5m) → ~0.57 lux on ground
- At 10m → ~0.14 lux (1/4 as bright)

---

## LINEAR ALGEBRA (SKELETAL ANIMATION)

### Linear Blend Skinning (LBS)
$$\mathbf{v}_{\text{skinned}} = \sum_{i=1}^{n} w_i \times \mathbf{M}_i \times \mathbf{M}_{i,\text{rest}}^{-1} \times \mathbf{v}_{\text{bind}}$$

- $\mathbf{v}_{\text{bind}}$ = vertex in bind pose (rest)
- $\mathbf{M}_{i,\text{rest}}$ = bone i rest matrix (inverse tells us bind space)
- $\mathbf{M}_i$ = bone i current pose matrix
- $w_i$ = vertex weight for bone i (∑$w_i$ = 1.0 per vertex)
- Result: deformed vertex position in world space

**Limitation:** Linear matrix interpolation loses volume at twists (candy wrapper)

---

### Dual Quaternion Skinning (DQS)
$$\hat{\mathbf{q}} = \frac{\sum_i w_i \hat{\mathbf{q}}_i}{\left| \sum_i w_i \hat{\mathbf{q}}_i \right|}$$

- $\hat{\mathbf{q}}_i$ = dual quaternion representation of bone transform
- Interpolation on unit DQ manifold (always valid transform)
- Preserves volume better than LBS

**Blender:** Available in Armature modifier settings (Deform Type)

---

### Transform Composition
$$\mathbf{p}_{\text{world}} = \mathbf{M}_{\text{object}} \times \mathbf{p}_{\text{object}}$$

- $\mathbf{M}_{\text{object}}$ = 4×4 transformation matrix (location + rotation + scale)
- Hierarchy: $\mathbf{p}_{\text{world}} = \mathbf{M}_1 \times \mathbf{M}_2 \times ... \times \mathbf{p}_{\text{local}}$

**Issue:** Scale in matrix requires orthonormalization; safest to apply (bake into vertices)

---

## ANIMATION MATHEMATICS

### NLA Strip Scale & Evaluation
$$\text{scale} = \frac{\text{strip\_duration}}{\text{action\_duration}}$$

$$\text{action\_frame} = \text{action\_frame\_start} + (f - \text{strip\_frame\_start}) \times \frac{1}{\text{scale}}$$

**Example:** Run action 54f, strip [288–432] (144f)
- scale = 144 / 54 = 2.67
- At scene frame f=350: action_frame = 0 + (350–288) × (1/2.67) = 23.2
- Character has played ~23 frames of 54-frame run

---

### Quaternion SLERP (Spherical Linear Interpolation)
$$\text{SLERP}(\mathbf{q}_1, \mathbf{q}_2, t) = \frac{\sin((1-t)\theta)}{\sin\theta} \mathbf{q}_1 + \frac{\sin(t\theta)}{\sin\theta} \mathbf{q}_2$$

$$\theta = \cos^{-1}(\mathbf{q}_1 \cdot \mathbf{q}_2)$$

- Used for bone rotation interpolation
- Always interpolates shortest path on unit quaternion sphere
- No gimbal lock (unlike Euler angles)

---

## BLENDER PYTHON (bpy) PATTERNS

### SKILL.md Rule 3: Transform Apply (Critical!)

```python
# WRONG: Scale and forget
obj.scale = (2.0, 2.0, 2.0)
# ... later code operates on unscaled mesh ...

# CORRECT: Apply immediately
obj.scale = (2.0, 2.0, 2.0)
with bpy.context.temp_override(selected_objects=[obj], active_object=obj):
    bpy.ops.object.transform_apply(scale=True)
# Now mesh vertices are baked; all systems see consistent scale
```

**Why:** Mesh-space vertices must be consistent with scale. Armature/physics/normals computed in object space.

---

### Create Object with Transform + Apply

```python
# Create cube (default size=2, so ±1 units)
bpy.ops.mesh.primitive_cube_add(size=2, location=(x, y, z))
obj = bpy.context.active_object
obj.name = "MyObject"

# Scale for desired half-extents
obj.scale = (sx, sy, sz)

# RULE 3: Apply immediately
with bpy.context.temp_override(selected_objects=[obj], active_object=obj):
    bpy.ops.object.transform_apply(scale=True)

# Now vertex bounds = ±sx, ±sy, ±sz in object space
```

---

### Verify Object Bounds

```python
def get_bounds(obj):
    """Return min/max for each axis after applying transforms."""
    if not obj.data.vertices:
        return None
    world_verts = [obj.matrix_world @ v.co for v in obj.data.vertices]
    xs = [v.x for v in world_verts]
    ys = [v.y for v in world_verts]
    zs = [v.z for v in world_verts]
    return {
        'x': (min(xs), max(xs)),
        'y': (min(ys), max(ys)),
        'z': (min(zs), max(zs))
    }

# Audit street light pole
pole_obj = bpy.data.objects["POLE_Lamp_01"]
bounds = get_bounds(pole_obj)
print(f"Pole Z: [{bounds['z'][0]:.1f}, {bounds['z'][1]:.1f}]")
# Expected: [0, 4.5] (ground to lamp head)
```

---

### NLA Strip Configuration

```python
# Add NLA track
nla_track = armature.animation_data.nla_tracks.new()
nla_track.name = "RunTrack"

# Add strip
action = bpy.data.actions.get("paramedic_run")
strip = nla_track.strips.new(
    name="run_beat1",
    start=288,  # Scene frame where strip starts
    action=action
)
strip.frame_start = 0  # Action frame to start from
strip.frame_end = strip.frame_start + 54  # Action duration
strip.scale = 144 / 54  # NLA scale

# Blending
strip.blend_in = 12  # 0.5s at 24fps
strip.blend_out = 8   # 0.33s

# Extrapolation: hold final frame after strip
strip.extrapolation = 'HOLD_FORWARD'
```

---

### Import GLB with Animations

```python
import bpy

# Import GLB file
bpy.ops.import_scene.gltf(
    filepath="path/to/model.glb",
    import_materials=True,
    import_cameras=False,
    import_lights=False
)

# Get the imported object
imported = bpy.context.selected_objects[0]
print(f"Name: {imported.name}")
print(f"Skeleton: {imported.find_armature().name if imported.type == 'MESH' else 'None'}")

# List imported actions
for action in bpy.data.actions:
    print(f"Action: {action.name} - {action.frame_range[1]:.0f}f")
```

---

### Blender 5.1: Querying Action Data

```python
# OLD (Blender 4.x): action.fcurves[0]
# NEW (Blender 5.1): action.layers[0].strips[0].channelbags[0].fcurves[0]

def get_fcurves_for_property(action, prop_name):
    """Return F-curves for a property (e.g., 'location')."""
    fcurves = []
    for layer in action.layers:
        for strip in layer.strips:
            for bag in strip.channelbags:
                for fc in bag.fcurves:
                    if fc.data_path.endswith(prop_name):
                        fcurves.append(fc)
    return fcurves

# Find all Hips.location X-channel curves
hips_x_curves = get_fcurves_for_property(action, "location.x")
```

---

## BLENDER RENDERING SETTINGS

### Eevee Render Engine Configuration

```python
scene = bpy.context.scene
eevee = scene.eevee

# Render settings
scene.render.engine = 'BLENDER_EEVEE_NEXT'  # or 'BLENDER_EEVEE'
scene.render.resolution_x = 1920
scene.render.resolution_y = 1080
scene.render.fps = 24
scene.render.frame_start = 0
scene.render.frame_end = 1871  # 1872 frames total

# Output
scene.render.filepath = "//render_####.png"  # #### = frame number
scene.render.image_settings.file_format = 'PNG'
scene.render.image_settings.compression = 15  # 0–15

# Eevee sampling
eevee.taa_render_samples = 128  # 128× temporal samples
eevee.taa_temporal_scale = 2.0

# Volumetrics
eevee.use_volumetric_shadows = True
eevee.volumetric_tile_size = '8x8'
eevee.volumetric_samples = 64

# Post-processing
eevee.use_bloom = True
eevee.bloom_threshold = 0.8
eevee.bloom_intensity = 0.25
eevee.bloom_radius = 6.5
eevee.bloom_knee = 0.5

eevee.use_motion_blur = True
eevee.motion_blur_shutter = 0.5

# Tone mapping
scene.display_settings.display_device = 'sRGB'
scene.view_settings.view_transform = 'Filmic'
scene.view_settings.exposure = 0.4
scene.view_settings.gamma = 1.0
```

---

### World/Volumetrics Setup

```python
world = bpy.data.worlds["World"]
world.use_nodes = True
nodes = world.node_tree.nodes
nodes.clear()

# Background
bg_node = nodes.new(type='ShaderNodeBackground')
bg_node.inputs['Color'].default_value = (0.1, 0.1, 0.12, 1.0)  # Dark night blue
bg_node.inputs['Strength'].default_value = 1.0

# Volume (participating media)
volume_node = nodes.new(type='ShaderNodeVolumeScatter')
volume_node.inputs['Color'].default_value = (0.92, 0.93, 0.97, 1.0)  # Cool grey
volume_node.inputs['Density'].default_value = 0.008
volume_node.inputs['Anisotropy'].default_value = 0.4  # Forward scatter (visible beams)

# Output
output_node = nodes.new(type='ShaderNodeOutputWorld')
world.node_tree.links.new(bg_node.outputs['Background'], output_node.inputs['Surface'])
world.node_tree.links.new(volume_node.outputs['Volume'], output_node.inputs['Volume'])
```

---

### Point Light (Street Lights)

```python
# Create point light
bpy.ops.object.light_add(type='POINT', location=(sx, sy, sz))
light_obj = bpy.context.active_object
light_obj.name = "LIGHT_Street_01"

light = light_obj.data
light.energy = 180  # Watts
light.color = (1.0, 0.92, 0.65)  # Warm sodium spectrum
light.use_shadow = True
light.shadow_soft_size = 0.5  # Penumbra radius
```

---

### Area Light (Animated Emergency Flasher)

```python
# Create area light
bpy.ops.object.light_add(type='AREA', location=(ax, ay, az))
light_obj = bpy.context.active_object
light_obj.name = "LIGHT_RED_FLASHER"

light = light_obj.data
light.energy = 800  # Watts (bright)
light.color = (1.0, 0.0, 0.0)  # Red
light.shape = 'SQUARE'
light.size = 0.5

# Animate energy: 800W ↔ 20W every 6 frames (2Hz)
anim_data = light_obj.animation_data_create()
action = bpy.data.actions.new("RED_FLASHER_action")
anim_data.action = action

# Energy F-curve
fcurve = action.fcurves.new(data_path="energy")
keyframes = [
    (0, 800),
    (6, 20),
    (12, 800),
    (18, 20),
    # ... repeat pattern for total 1872 frames
]
for f, e in keyframes:
    kf = fcurve.keyframe_points.insert(f, e)
    kf.interpolation = 'LINEAR'
```

---

## PHYSICS CONVERSIONS

### Focal Length ↔ Field of View

$$\text{FOV} = 2 \times \arctan\left(\frac{\text{sensor\_width}}{2 \times f}\right)$$

- $f$ = focal length (mm)
- sensor_width = 36mm (full-frame) or 24mm (APS-C)

**Our project (35mm full-frame):**
- 28mm → FOV ≈ 73° (wide, establishing shot)
- 35mm → FOV ≈ 63° (normal)
- 50mm → FOV ≈ 47° (standard)
- 85mm → FOV ≈ 29° (telephoto, compressed)

---

### Depth of Field (Defocus Circle)

$$c = \frac{f \times D \times |d - D|}{N \times d \times D}$$

- $c$ = circle of confusion (blur radius, mm)
- $f$ = focal length (mm)
- $D$ = focus distance (m)
- $d$ = subject distance (m)
- $N$ = f-number (aperture, e.g., f/2.8 → N=2.8)

**Our CAM_E_ASSESS_CU:** 85mm, f/2.8, focus=2.2m
- At 2.2m: blur = 0 (sharp)
- At 1.5m: blur increases (paramedic face edge blur)
- At 10m: blur maximum (background completely soft)

---

## CHECKLIST: RENDER COMMAND

```bash
# Render 1872 frames in Blender
# Use: -b (background), -s (start), -e (end), -a (render animation)
blender -b scene.blend -s 1 -e 1872 -a
```

**Estimated time:** 
- Per frame: 1.0–1.5 seconds (GPU)
- Total: 1,872–2,808 seconds
- = 31–47 minutes (batch render on high-end GPU)
- Output: `render_0001.png`, `render_0002.png`, ..., `render_1872.png`

**Assemble to video (FFmpeg):**
```bash
ffmpeg -framerate 24 -pattern_type glob -i 'render_*.png' \
  -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2" \
  -pix_fmt yuv420p -c:v libx264 -crf 23 output.mp4
```

---

*Print this page for exam-day quick reference.*
