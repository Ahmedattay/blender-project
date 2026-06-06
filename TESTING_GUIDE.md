# Blender Animation Project — Testing & Playback Guide
## Traffic Accident at Night – Ambulance Emergency Response Simulation

---

## 1. Opening Your Project

1. Launch **Blender 5.1**
2. Go to **File → Open**
3. Navigate to: `C:\Users\mahmo\OneDrive\المستندات\blender\Project_test1.blend`
4. Click **Open Blender File**

---

## 2. Understanding the Scene Layout

When you open the file, your **Outliner** (top-right panel) will show these collections:

| Collection | What's inside |
|---|---|
| `SCN1_Ambulance_Arrival` | Ambulance body (AMB_Body), cabin (AMB_Cabin), Support paramedic rig (EMS_Paramedic_Support) + Support_Body/Shirt/Pants/Shoes meshes |
| `EMS_Characters` → `Paramedic` | Lead paramedic rig + Ch16 clothing meshes |
| `EMS_Characters` → `Patient` | Patient rig + Prisoner mesh |
| `EMS_Blocking` | Orange/blue helper empties (staging anchors, invisible in render) |
| `EMS_Cameras` | 10 pre-built cinematic cameras (CAM_A through CAM_J) |
| `Collection` | Environment: buildings, BASE, BASE.042 |

---

## 3. Playing Scenario 1 (Ambulance Arrival & Patient Assessment)

### Step 1 — Set the playback range
Scenario 1 covers **frames 0–840** (0 to 35 seconds at 24fps).

The scene frame range is already set to `0–840`. To verify:
- Look at the **Timeline** at the bottom — the green bar should span from **0** to **840**
- The start/end frame numbers are in the bottom-left of the Timeline

### Step 2 — Go to frame 0
Press `Shift + Left Arrow` or type `0` in the **Current Frame** box in the Timeline.

### Step 3 — Play the animation
Press **Spacebar** to play.

What you should see:
- **Frames 0–144 (0–6s)**: Ambulance is off-screen to the right (X=30)
- **Frames 144–240 (6–10s)**: Ambulance enters and decelerates into position
- **Frame 240 (10s)**: Ambulance stops with rear door at the correct position
- **Frames 288–432 (12–18s)**: Both paramedics run toward the patient
- **Frames 432–624 (18–26s)**: Lead paramedic kneels beside the patient
- **Frames 624–840 (26–35s)**: Lead performs patient assessment; Support prepares stretcher

Press **Spacebar** again to stop.

---

## 4. Checking NLA Animation Tracks

To inspect how animations are organized:

1. In the **Editor Type** selector (top-left of any panel), switch a panel to **Nonlinear Animation**
2. Select any armature in the scene (click it in the Viewport)
3. The NLA editor will show its tracks and strips

### NLA Track Map — Scenario 1

**Lead Paramedic (`Armature`)**
```
S1_Lead_Idle_InAmb        [0–288]    Frozen standing pose (inside ambulance)
S1_Lead_Approach          [288–432]  Running approach
S1_Lead_Assessment        [432–840]  Kneel down → Assessment hold
```

**Support Paramedic (`EMS_Paramedic_Support`)** — real skinned character from `paramedic_run-with_sckin.fbx`
```
S1_Support_Idle_InAmb        [0–288]    Frozen standing pose (inside ambulance)
S1_Support_Approach          [288–432]  Running approach to stretcher zone
S1_Support_Prepare_Stretcher [432–840]  Stretcher preparation (kneel action)
```

**Patient (`Armature.001`)**
```
S1_Patient_Passive        [0–840]    Looping laying animation (repeat=16.47×)
```

---

## 5. Checking Object Positions at Key Frames

Use these steps to verify positions at any moment:

1. Press `G` key on keyboard → no, instead: type a frame number in the **Current Frame** box
2. Select an object in the viewport (click it)
3. Press `N` in the viewport to open the **Item** panel (right sidebar)
4. Look at **Location X/Y/Z** — these update as you move frames

**Expected positions at frame 432 (18s):**
| Object | X | Y | Z |
|---|---|---|---|
| Lead Paramedic | -2.0 | 0.5 | 0.0 |
| EMS_Paramedic_Support | 3.2 | -1.5 | 0.0 |
| Patient | -2.456 | 0.107 | 0.0 |
| Ambulance Body | 11.0 | 0.0 | 1.15 |

---

## 6. Checking Mesh Transforms (SKILL.md Rule 3)

All character and environment meshes **must** have:
- Rotation: (0°, 0°, 0°)
- Scale: (1.0, 1.0, 1.0)

To verify a mesh:
1. Click the mesh in the viewport
2. Press `N` to open the sidebar
3. Check the **Transform** section

Or run this quick audit in the **Scripting workspace**:
```python
import bpy
for obj in bpy.data.objects:
    if obj.type not in ('MESH', 'ARMATURE'):
        continue
    rot = tuple(round(float(c),3) for c in obj.rotation_euler)
    scl = tuple(round(float(c),3) for c in obj.scale)
    if rot != (0.0,0.0,0.0) or scl != (1.0,1.0,1.0):
        print(f"PROBLEM: {obj.name} rot={rot} scl={scl}")
print("Audit complete")
```

---

## 7. Verifying the Ambulance Animation

To check the ambulance moves correctly:

1. Click `AMB_Body` in the Outliner
2. Go to frame 0 — confirm Location X = **30.0** (off-screen)
3. Go to frame 240 — confirm Location X = **11.0** (parked)
4. The cabin (`AMB_Cabin`) is parented to the body, so it follows automatically

---

## 8. Viewing a Specific Camera Shot

The scene has 10 pre-built cameras in the `EMS_Cameras` collection.

To preview a camera:
1. Click the camera name in the Outliner (e.g. `CAM_B_ARRIVAL`)
2. Press `Numpad 0` — this switches the viewport to camera view
3. Press `Spacebar` to play and see the shot

**Camera reference by shot:**
| Camera | Frames | Purpose |
|---|---|---|
| CAM_A_ESTABLISH | 0–144 | Wide establishing — night environment |
| CAM_B_ARRIVAL | 144–288 | Ambulance approaches and parks |
| CAM_C_DISMOUNT | 288–432 | Paramedics exit |
| CAM_D_APPROACH | 288–432 | Running toward patient |
| CAM_E_ASSESS | 432–840 | Kneeling assessment (low angle) |

---

## 9. Running the Full Autonomous Audit via Script

Open the **Scripting** workspace in Blender and run:

```python
import bpy, math

scene = bpy.context.scene
fps = scene.render.fps
print(f"\n=== PROJECT STATUS ===")
print(f"FPS: {fps}")
print(f"Scene range: {scene.frame_start}–{scene.frame_end} ({scene.frame_end/fps:.1f}s)")
print(f"\n=== ARMATURES ===")
for obj in bpy.data.objects:
    if obj.type != 'ARMATURE': continue
    ad = obj.animation_data
    nla_tracks = len(ad.nla_tracks) if ad else 0
    print(f"  {obj.name}: bones={len(obj.data.bones)}, NLA_tracks={nla_tracks}, loc={[round(float(v),2) for v in obj.location]}")

print(f"\n=== NLA STRIPS ===")
for obj in bpy.data.objects:
    if obj.type != 'ARMATURE' or not obj.animation_data: continue
    for tr in obj.animation_data.nla_tracks:
        for st in tr.strips:
            act = st.action.name if st.action else "None"
            print(f"  [{obj.name}] {tr.name} → {st.name} ({act}) f{st.frame_start:.0f}–{st.frame_end:.0f}")

print(f"\n=== TRANSFORM CHECK ===")
problems = 0
for obj in bpy.data.objects:
    if obj.type not in ('MESH','ARMATURE'): continue
    rot = tuple(round(float(c),3) for c in obj.rotation_euler)
    scl = tuple(round(float(c),3) for c in obj.scale)
    if rot != (0.0,0.0,0.0) or scl != (1.0,1.0,1.0):
        print(f"  !! {obj.name}: rot={rot} scl={scl}")
        problems += 1
if problems == 0:
    print("  All transforms clean (SKILL.md PASS)")
```

---

## 10. Character Assets — Where They Come From

All animations come from your Mixamo downloads, converted from ASCII FBX to GLB:

| Original FBX | GLB Converted File | Blender Action Name |
|---|---|---|
| `paramedic_kneel.fbx` | `paramedic_kneel.glb` | `paramedic_paramedic_kneel_act_01` |
| `paramedic_run-with_sckin.fbx` | `paramedic_run-with_sckin.glb` | `paramedic_paramedic_run_act_01` |
| `paramedic_carry.fbx` | `paramedic_carry.glb` | `paramedic_paramedic_carry_act_01` |
| `patient_laying.fbx` | `patient_laying.glb` | `patient_patient_laying_act_01` |
| `patient_being_carried-with_skin.fbx` | `patient_being_carried-with_skin.glb` | `patient_patient_being_carried_act_01` |

**Source folders:**
- Raw FBX: `D:\COLLAGE\Spring 26\Computer Graphics\Project\assets\characters\paramedic\`
- Raw FBX: `D:\COLLAGE\Spring 26\Computer Graphics\Project\assets\characters\patient\`
- Converted GLB: `D:\COLLAGE\Spring 26\Computer Graphics\Project\assets\characters\_converted_glb\`

> The `_converted_glb` folder is safe to keep — these are the actual files imported into Blender.
> The original `.fbx` files are also kept as source backups.

---

## 11. Common Issues and Fixes

### Animation doesn't play / characters freeze
- Make sure NLA editor has the rig selected
- Check that the active action slot is cleared (it should be None; NLA drives everything)
- In the NLA editor, tracks should NOT be muted (no eye icon closed)

### Ambulance doesn't move
- Select `AMB_Body` → check it has a keyframe animation action (`AMB_BodyAction.001`)
- In the **Dope Sheet**, switch to **Action Editor** and select `AMB_Body`

### Character mesh and rig look disconnected
- Select the mesh → check its **Armature modifier** points to the correct rig
- Lead meshes (`Ch16_Body1`, etc.) → must point to `Armature`
- Support meshes (`Support_Body`, `Support_Shirt`, etc.) → must point to `EMS_Paramedic_Support`
- Patient mesh (`Prisoner`) → must point to `Armature.001`

### Frame range is wrong (can't see full animation)
- In the Timeline header, set Start: `0`, End: `840`
- Or run: `bpy.context.scene.frame_start = 0; bpy.context.scene.frame_end = 840`

---

## 12. What Comes Next — Scenario 2

Scenario 2 (Patient Extraction & Loading, frames 840–1872) uses:
- `paramedic_paramedic_carry_act_01` for lift/carry/load phases
- `patient_patient_being_carried_act_01` for patient transfer
- Same master rigs (`Armature` and `Armature.001`)

To preview Scenario 2 when ready:
- Set Timeline End to **1872** (78s)
- Press Spacebar from frame 840

---

*Last generated: 2026-06-06 | Project: Traffic Accident at Night – Ambulance Emergency Response*
