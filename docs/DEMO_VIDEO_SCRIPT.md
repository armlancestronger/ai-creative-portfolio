# Demo Video Script — 75-Second ComfyUI Pipeline Walkthrough

## Purpose
A concise screen-recorded walkthrough demonstrating end-to-end AI video generation using ComfyUI, showcasing character consistency, multi-ControlNet scene composition, and Impact Pack detail refinement.

---

## SECTION 1: Character Identity Lock (0:00 – 0:20)

**Screen:** ComfyUI canvas showing the Character Identity Lock workflow.

**Voiceover:**
> "This is my character identity pipeline. I load a base checkpoint, attach an IP-Adapter with a single reference headshot, and lock facial identity using a CLIP-Vision embedding. Every generation from this point forward carries the same face — no retraining, no LoRA fine-tuning required for initial lock."

**On-screen action:** Show the IP-Adapter node wired to the reference image, then generate 3-4 portrait variations proving identity holds across different angles and lighting.

---

## SECTION 2: Scene Composition with Multi-ControlNet (0:20 – 0:45)

**Screen:** Switch to the Scene Composition workflow.

**Voiceover:**
> "For scene composition, I stack multiple ControlNet layers — OpenPose for body positioning, depth map for spatial layout, and a canny edge pass for architectural detail. The character identity carries forward from the previous stage via the shared latent pipeline."

**On-screen action:** Show each ControlNet preprocessor output side-by-side (pose skeleton, depth map, canny edges), then the composed scene generation. Highlight how the character's face remains consistent in the new environment.

---

## SECTION 3: Detail Refinement with Impact Pack (0:45 – 1:05)

**Screen:** Switch to the Detector-Detailer workflow.

**Voiceover:**
> "Final pass — Impact Pack's detector-detailer. An ONNX-accelerated SAM model segments the face region automatically, and a dedicated inpainting pass at higher resolution refines skin texture, eye detail, and expression. This runs in under 4 seconds on my RTX setup with 32 gigs of RAM."

**On-screen action:** Show the SAM mask overlay on the face region, then the before/after comparison of the detail pass. Zoom in on eyes and skin texture improvement.

---

## SECTION 4: Closing (1:05 – 1:15)

**Screen:** Side-by-side grid of all outputs — reference image, identity-locked variations, composed scene, detail-refined final.

**Voiceover:**
> "From reference photo to production-ready frame — fully automated, fully reproducible. Every node, every setting is documented in my workflow guide. Marc Doane — AI Creative Artist."

---

## Recording Notes

- **Resolution:** 1920x1080 minimum
- **Format:** MP4 / H.264
- **Audio:** Clean voiceover, no background music needed
- **Screen capture:** OBS Studio or similar
- **Tip:** Pre-generate all outputs before recording so the walkthrough flows smoothly without waiting for renders
- **Total runtime target:** 75 seconds (1:15)
