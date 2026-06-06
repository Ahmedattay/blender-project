# Viva Preparation Study Guide Index
## Virtual Ambulance Emergency Response Simulation — Final Project

---

## HOW TO USE THIS PACKAGE

You have 5 comprehensive study documents totaling **~6,500 lines of material**. Here's how to use each:

### 📚 DOCUMENT MATRIX

| Document | Size | Purpose | When To Use | Time Required |
|----------|------|---------|-------------|---------------|
| **VIVA_CONDENSED** | 380 lines | Quick facts & Q&A | Day-of presentation (10 min) | 10 minutes |
| **VIVA_FLASHCARDS** | 20 cards | Memorize key concepts | Study in bed, short sessions | 30 minutes |
| **VIVA_PRACTICE_QUIZ** | 60 questions | Self-test knowledge | Weeks before, mid-study | 2 hours |
| **VIVA_TIMELINE_MISTAKES** | 40 pages | Scene breakdown + defenses | Study common errors, practice responses | 1.5 hours |
| **VIVA_REFERENCE_FORMULAS** | Technical reference | Formulas, code, settings | During preparation, day-of lookup | As needed |
| **VIVA_PREPARATION_HANDBOOK** | 4000+ lines | Comprehensive masterclass | Deep learning, all sections | 4–6 hours |

---

## STUDY SCHEDULE (Recommended)

### WEEK 1: Foundation (4 hours)
1. Read **VIVA_CONDENSED.md** (10 min) — understand overall structure
2. Study **VIVA_FLASHCARDS.md** (30 min) — memorize 20 key concepts
3. Read **VIVA_TIMELINE_MISTAKES.md** Section 1 (45 min) — understand scene beats
4. Complete **VIVA_PRACTICE_QUIZ.md** Part A (20 min) — test easy questions
5. Read **VIVA_REFERENCE_FORMULAS.md** sampling (30 min) — familiarize with formulas

### WEEK 2: Deep Learning (6 hours)
1. Read **VIVA_PREPARATION_HANDBOOK.md** Sections 1–4 (2 hours) — theory & concepts
2. Read **VIVA_TIMELINE_MISTAKES.md** Section 2 (45 min) — learn common mistakes & defenses
3. Complete **VIVA_PRACTICE_QUIZ.md** Parts B–D (1.5 hours) — test medium/difficult questions
4. Re-study **VIVA_FLASHCARDS.md** (30 min) — reinforce memorization
5. Read **VIVA_REFERENCE_FORMULAS.md** code sections (1 hour) — understand implementation

### WEEK 3: Advanced Topics (4 hours)
1. Read **VIVA_PREPARATION_HANDBOOK.md** Sections 5–10 (2 hours) — Blender specifics, animation, mock exam
2. Complete **VIVA_PRACTICE_QUIZ.md** Part E–F (1 hour) — debugging and essay questions
3. Practice answering **VIVA_TIMELINE_MISTAKES.md** defenses aloud (30 min)
4. Create flash cards for weak areas (30 min)

### DAY BEFORE: Review (1.5 hours)
1. Read **VIVA_CONDENSED.md** (10 min) — quick refresh
2. Quiz yourself on **VIVA_FLASHCARDS.md** (30 min)
3. Review top 10 mistakes from **VIVA_TIMELINE_MISTAKES.md** (20 min)
4. Skim **VIVA_REFERENCE_FORMULAS.md** rendering section (20 min)
5. Sleep well! 🌙

### DAY OF PRESENTATION: Final Prep (30 minutes)
1. Read **VIVA_CONDENSED.md** (10 min) — refresh project facts
2. Skim **VIVA_REFERENCE_FORMULAS.md** rendering equation + code patterns (10 min)
3. Review your personal weak-point notes (10 min)
4. Take 3 deep breaths. You're ready. ✓

---

## DOCUMENT BREAKDOWN

### 1️⃣ VIVA_CONDENSED.md (Quick Reference)
**Best for:** Last 10 minutes before presenting; quick facts lookup

**Contains:**
- Project facts table (duration, characters, objects, lights, cameras)
- Decision justifications (software choices explained)
- Core CG concepts (compressed table format)
- Top 20 discussion Q&A
- 4 SKILL.md rules with explanations
- Rendering equation
- Quick timeline (9-beat summary)

**Read if:** You need quick facts or are short on time

---

### 2️⃣ VIVA_FLASHCARDS.md (Memory Cards)
**Best for:** Daily study; memorizing 20 core concepts

**Contains 20 cards with:**
1. Animation pipeline (7 steps)
2. Inverse square law formula + application
3. Diffuse lighting equation + Lambertian explanation
4. PBR workflow (Base Color, Metallic, Roughness)
5. Blender 5.1 Action API (layered system)
6. Linear Blend Skinning formula
7. Fresnel effect (Schlick's approximation)
8. Transform Apply (SKILL.md Rule 3)
9. NLA strip scale mathematics
10. Bloom in night scenes (why important)
11. Volumetric rendering (density=0.008 explained)
12. Tone mapping (Filmic)
13. Shadow maps (1024×1024 resolution explained)
14. Rasterization vs Ray Tracing (comparison table)
15. Blender unit system (1 unit = 1 meter)
16. Fresnel vs Metallic (Cook-Torrance)
17. Quaternions vs Euler (gimbal lock prevention)
18. Vertex groups & weight painting
19. F-curve interpolation types
20. DOF (Depth of Field) calculation

**Read if:** You have 30 minutes for memory work

---

### 3️⃣ VIVA_PRACTICE_QUIZ.md (Self-Testing)
**Best for:** Testing knowledge at different difficulty levels; identifying weak areas

**Contains 60 questions in 6 parts:**
- **Part A:** 10 easy multiple choice (project facts)
- **Part B:** 10 medium multiple choice (concepts & math)
- **Part C:** 10 short answer (explain concepts)
- **Part D:** 4 long answer (derivations & complexity)
- **Part E:** 3 technical debugging (real errors + fixes)
- **Part F:** 1 essay (FBX import problem walkthrough)

**Scoring guide:**
- 80%+ on Parts A–B: Ready for viva
- <80%: Review corresponding HANDBOOK section
- 0% on Part E–F: Study VIVA_REFERENCE_FORMULAS code section

**Read if:** You want to test yourself before the viva (recommended!)

---

### 4️⃣ VIVA_TIMELINE_MISTAKES.md (Scene Breakdown + Defense)
**Best for:** Understanding pacing, scene beats, and preparing rebuttals

**Contains:**
- **Scene-by-scene breakdown** (9 beats, 0–78 seconds)
  - Beat 1: Establishing shot (0–6s)
  - Beat 2: Ambulance arrival (6–12s)
  - Beat 3: Approach & kneel (12–26s)
  - ... (9 beats total)
  - Each beat: camera, animation, lighting, audio cues

- **13 common mistakes** with:
  - Root cause explanation
  - Red flag (what professor will challenge)
  - Your defense (how to respond)
  - Example code

  Mistakes covered:
  1. Root motion drift (teleporting characters)
  2. Foot sliding (gliding on ice)
  3. Animation jank at transitions
  4. Wrong animation for context
  5. Night scene too dark
  6. Unrealistic light falloff
  7. Flashers look fake
  8. Scale not applied
  9. Animations don't import (FBX problem)
  10. NLA strips misaligned
  11. Confusing material vs texture
  12. Confusing Eevee approximations
  13. Incorrect inverse square law

- **Pre-viva checklist** (10-point verification)

**Read if:** You want to avoid common pitfalls and prepare rebuttals

---

### 5️⃣ VIVA_REFERENCE_FORMULAS.md (Technical Reference)
**Best for:** Looking up formulas, code snippets, rendering settings

**Contains:**
- **Rendering Equations** (6 formulas)
  - Rendering equation (Kajiya)
  - Cook-Torrance BRDF
  - GGX distribution
  - Schlick's Fresnel
  - Lambertian diffuse
  - Inverse square law

- **Linear Algebra** (skeletal animation, 3 formulas)
  - Linear Blend Skinning
  - Dual Quaternion Skinning
  - Transform composition

- **Animation Math** (2 formulas)
  - NLA strip scale & evaluation
  - Quaternion SLERP

- **Blender Python (bpy) Code Patterns** (7 code blocks)
  - SKILL.md Rule 3: Transform apply
  - Object creation + transform + apply
  - Bounds verification function
  - NLA strip configuration
  - GLB import with animations
  - Blender 5.1 Action API querying
  - Eevee render settings
  - World volumetrics setup
  - Point light (street lights)
  - Area light (animated flashers)
  - Import GLB code

- **Physics Conversions** (3 formulas)
  - Focal length ↔ FOV
  - Depth of Field calculation
  - Render command + timing

**Use as:** Print & keep during viva; lookup specific formulas during discussion

---

### 6️⃣ VIVA_PREPARATION_HANDBOOK.md (Comprehensive Masterclass)
**Best for:** Deep learning; understanding all aspects of the project

**Contains 10 sections (~4000 lines):**

**Section 1: Project Understanding (3 levels each)**
- Level 1 (beginner): Simplified explanation
- Level 2 (technical): How it works
- Level 3 (professor): Defense + challenges
- Topics: Scene setup, animation system, lighting, render pipeline, teamwork

**Section 2: CG Concepts Masterclass (30+ concepts)**
- Rendering, shading, lighting, materials, animation, geometry, optimization, workflow
- Each concept: definition, why it exists, project application, Q&A

**Section 3: 100 Discussion Questions (Q1–Q100)**
- 5 topics × 20 questions each
- Organized from foundational to advanced
- Project-specific examples in every answer
- Formulas and mathematics included

**Section 4: Defense Questions (4 difficulty levels)**
- Easy (warm-up): Basic project facts
- Medium: Why you chose certain approaches
- Difficult: Technical problem-solving
- Very difficult: Edge cases, optimizations, alternatives

**Section 5: Blender Masterclass**
- Scene hierarchy, collections, naming conventions
- SKILL.md rules (4 critical rules with explanations)
- Python scripting (apply transforms, verify bounds, audit logic)
- Version differences (Blender 4.x vs 5.1)

**Section 6: Mixamo & Skeletal Animation**
- Mixamo ecosystem (rigged models, animations)
- Skeleton topology (65-bone standard)
- FBX → GLB conversion (why, how, when)
- Weight painting, skinning, deformation

**Section 7: Lighting & Shading Masterclass**
- Three-point lighting in emergency scene
- Street lights (motivation, spectrum, placement)
- Emergency flashers (animation, realism, bloom)
- Volumetrics (fog, atmospheric effects)
- Material system (Principled BSDF, PBR workflow)

**Section 8: Animation System Masterclass**
- NLA (Non-Linear Animation) workflow
- Strip blending, scaling, extrapolation
- Root motion vs in-place animation
- Transitions, state machines, pose retention
- NLA evaluation mathematics

**Section 9: Mock 50-Question Oral Exam**
- Progressive difficulty (beginner → expert)
- Professor-style questioning
- Covers all project aspects
- Ideal for practice with a peer

**Section 10: 10-Minute Cheat Sheet**
- Definitions, quick answers
- Formulas on one page
- Facts table (memorize these!)
- Timeline (9 beats, key timings)

**Read if:** You have time for comprehensive learning (4–6 hours)

---

## QUICK LOOKUP TABLE

**"What question might I get asked?"** → **Where to find the answer:**

| Question | Document | Location |
|----------|----------|----------|
| "What is your project duration?" | VIVA_CONDENSED | Project facts table |
| "How does inverse square law work?" | VIVA_FLASHCARDS | Card #2 or VIVA_REFERENCE_FORMULAS |
| "What would you improve?" | VIVA_PREPARATION_HANDBOOK | Section 4, Defense questions |
| "Why did you use Eevee?" | VIVA_CONDENSED | Decision justifications |
| "Explain Linear Blend Skinning" | VIVA_REFERENCE_FORMULAS | Linear Algebra section |
| "What causes foot sliding?" | VIVA_TIMELINE_MISTAKES | Mistake #2 |
| "Walk me through your animation pipeline" | VIVA_FLASHCARDS | Card #1 or VIVA_PREPARATION_HANDBOOK Section 6 |
| "How do you apply transforms correctly?" | VIVA_TIMELINE_MISTAKES | Mistake #8 or VIVA_REFERENCE_FORMULAS |
| "What is the rendering equation?" | VIVA_REFERENCE_FORMULAS | Rendering equations section |
| "Describe a technical problem you solved" | VIVA_PREPARATION_HANDBOOK | Section 3, Q1–Q100 |

---

## PRINTED REFERENCE CARDS (Recommended)

Consider printing these for exam-day quick reference:

1. **Print VIVA_CONDENSED.md** (1 page) — Keep in hand during presentation
2. **Print VIVA_FLASHCARDS.md** (10 pages) — Flash card set for memorization
3. **Print VIVA_REFERENCE_FORMULAS.md** (15 pages) — Rendering equations + code
4. **Make notes** on VIVA_TIMELINE_MISTAKES.md (10 pages) — Defensive responses

---

## TEST YOURSELF CHECKLIST

- [ ] Can I state 5 project facts (duration, character count, object count, lights, cameras) from memory?
- [ ] Can I name all 10 cameras and their focal lengths?
- [ ] Can I draw the NLA strip scale formula and calculate for our run clip?
- [ ] Can I explain why Eevee vs Cycles?
- [ ] Can I describe the 3-beat animation for "extract patient"?
- [ ] Can I explain SKILL.md Rule 3 and its consequence?
- [ ] Can I state the rendering equation and identify each term?
- [ ] Can I identify the 3 common mistakes in animation setups?
- [ ] Can I explain inverse square law with a real-world example?
- [ ] Can I defend one weakness in my project?

**If you can answer 8/10, you're ready for the viva.** 🎓

---

## VIVA DAY MORNING ROUTINE

1. **6:00 AM** — Wake up, light breakfast
2. **6:30 AM** — Read VIVA_CONDENSED.md once (10 min)
3. **6:45 AM** — Quiz yourself on VIVA_FLASHCARDS (15 min)
4. **7:00 AM** — Skim VIVA_REFERENCE_FORMULAS rendering section (15 min)
5. **7:20 AM** — Shower, get dressed, review notes (20 min)
6. **7:45 AM** — Arrive early, take 3 deep breaths (5 min)
7. **8:00 AM** — **Viva time. You've got this! 💪**

---

## QUICK REFERENCE: Project Facts (Memorize These)

- **Duration:** 78 seconds (1,872 frames @ 24fps)
- **Scenario 1:** Ambulance arrival + assessment (0–35s)
- **Scenario 2:** Patient extraction + loading (35–78s)
- **Characters:** 1 lead paramedic, 1 support paramedic, 1 patient
- **Scene objects:** 271 total
- **Armatures:** 3 (lead, support, patient)
- **Skeleton:** Mixamo 65-bone humanoid (prefix `mixamorig:`)
- **Animation clips:** 5 (run, kneel, carry, patient_lay, patient_carry)
- **Lights:** 13 total (7 street, 2 flashers, 2 headlights, 1 fill, 1 rim)
- **Cameras:** 10 cinematic cameras (28–85mm focal lengths)
- **Render engine:** Blender Eevee (rasterization, ~1.0–1.5s/frame)
- **Output:** 1920×1080, 24fps, PNG sequence, 128 TAA samples
- **Post-processing:** Bloom, motion blur, Filmic tone mapping, volumetrics

---

*This study guide package is complete. Print this index, bookmark all 6 documents, and you're ready for viva success. Good luck! 🎬*
