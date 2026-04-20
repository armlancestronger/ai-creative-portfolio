# ComfyUI Workflow Guide  AI Creative Portfolio

## Overview

This document describes the end-to-end ComfyUI pipelines used to produce cinematic AI video with consistent character identity across shots. Every workflow is built for reproducibility  node graphs, model paths, and parameter ranges are documented so any team member can replicate results.

---

## Core Pipeline Architecture

### 1. Character Identity Lock Pipeline

**Purpose:** Maintain a single character's face, build, and wardrobe across 50+ frames with zero identity drift.

**Nodes & Flow:**

1. **IP-Adapter (Plus Face)**  Injects a reference face embedding at strength 0.850.92. Locked to a single reference image per character.
2. **ControlNet (OpenPose + Depth)**  Dual-stack control. OpenPose governs body pose; Depth map preserves spatial consistency between foreground and background.
3. **LoRA (character-specific)**  Fine-tuned LoRA (rank 3264) trained on 2040 curated images of the target character. Applied at weight 0.70.8 to avoid overfit artifacts.
4. **KSampler**  Euler a, 2835 steps, CFG 7.07.5. Deterministic seed per shot, varied per angle.
5. **VAE Decode  Preview**

**Key Parameters:**

| Parameter | Range | Notes |
|-----------|-------|-------|
| IP-Adapter strength | 0.850.92 | Higher = stronger likeness, risk of rigidity |
| ControlNet OpenPose | 0.600.75 | Lower for natural motion |
| ControlNet Depth | 0.450.55 | Prevents background collapse |
| LoRA weight | 0.700.80 | Above 0.85 causes overfit haloing |
| CFG Scale | 7.07.5 | Sweet spot for detail vs. coherence |

---

### 2. Scene Composition Pipeline

**Purpose:** Generate multi-character scenes with independent identity control per subject.

**Nodes & Flow:**

1. **Dual IP-Adapter**  Two IP-Adapter nodes, each with separate reference images and regional masks.
2. **Regional Conditioning**  Splits the latent space into zones; each zone receives its own character prompt + IP-Adapter embedding.
3. **ControlNet (Canny + Depth)**  Canny edges for architectural/environmental detail; Depth for layered scene composition.
4. **Impact Pack Detector-Detailer**  Post-generation face correction. Uses YOLO face detection  SAM segmentation  targeted inpainting at 0.350.45 denoise.
5. **Upscale (4x-UltraSharp)**  Final 4x upscale with tiled VAE to manage VRAM on 812 GB GPUs.

---

### 3. Video Generation Pipeline

**Purpose:** Produce 35 second cinematic clips with temporal coherence.

**Nodes & Flow:**

1. **AnimateDiff**  Motion module v3, 16-frame batches at 8 fps.
2. **IP-Adapter (temporal consistency)**  Same reference image applied per-frame with strength scheduling (0.85 frame 1, ramp to 0.90 by frame 16).
3. **ControlNet (OpenPose sequence)**  Pre-extracted pose sequence from reference video. Applied per-frame.
4. **Context Scheduling**  Sliding window of 16 frames with 4-frame overlap for smooth transitions.
5. **Frame Interpolation (RIFE)**  2x interpolation to output 16 fps.
6. **Video Combine**  Assembled into MP4 with ffmpeg, H.264, CRF 18.

---

## Troubleshooting Quick Reference

| Symptom | Root Cause | Fix |
|---------|-----------|-----|
| Face drift between shots | IP-Adapter strength too low or wrong model | Use IP-Adapter Plus Face, strength >= 0.85 |
| Rigid/frozen expressions | IP-Adapter strength too high | Reduce to 0.85, add slight prompt variation |
| Background bleeding into subject | ControlNet Depth too low | Increase Depth strength to 0.50+ |
| Overfit halo artifacts around face | LoRA weight too high | Reduce LoRA to 0.700.75 |
| VRAM OOM on upscale | Untiled VAE decode | Enable tiled VAE, tile size 512 |
| AnimateDiff flickering | No context scheduling | Add sliding window, 4-frame overlap |
| SAM segmentation miss | Wrong detector model | Switch to YOLO v8 face, confidence 0.7 |

---

## Environment

- **OS:** Windows 11
- **GPU:** NVIDIA (812 GB VRAM tested)
- **RAM:** 32 GB
- **Python:** 3.11
- **ComfyUI:** Latest stable
- **Key Custom Nodes:** Impact Pack, ComfyUI-AnimateDiff-Evolved, ComfyUI-IP-Adapter-Plus, ComfyUI-ControlNet-Aux, ComfyUI-ONNX

---

## Contact

Marc Doane  marcdoane0116@outlook.com
