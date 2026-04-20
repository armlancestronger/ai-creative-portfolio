# ai-creative-portfolio
AI Artist Portfolio - ComfyUI workflows, character consistency pipelines, and cinematic video generation demos
# AI Creative Artist Portfolio — Marc Doane

**ComfyUI Pipeline Engineer | Character Consistency Specialist | Cinematic AI Video Generation**

---

## About Me

I'm an AI Software Engineer and ComfyUI workflow architect specializing in **character-consistent narrative video generation**. My pipelines deliver reproducible, production-grade outputs using IP-Adapter face-locking, multi-ControlNet scene composition, and ONNX-accelerated detector-detailer refinement — all running locally on consumer hardware (RTX 3060 / 32 GB RAM).

I build end-to-end creative workflows that solve the hardest problem in AI video: **maintaining character identity across multiple shots, scenes, and lighting conditions**.

---

## Core Technical Stack

| Component | Implementation |
|-----------|---------------|
| **Base Models** | SDXL 1.0, Juggernaut XL v9, RealVisXL V4.0 |
| **Face Locking** | IP-Adapter FaceID Plus V2 (weight 0.80) |
| **Pose Control** | ControlNet OpenPose (strength 0.80) |
| **Scene Composition** | Multi-ControlNet: Depth (0.70) + Canny (0.50) + OpenPose (0.80) |
| **Face Refinement** | UltralyticsDetectorProvider > SAMLoader (ONNX) > DetailerForEach |
| **Upscaling** | 4x-UltraSharp ESRGAN |
| **LoRA Integration** | Custom LoRA stacking (0.65-0.75 weight range) |
| **Sampling** | DPM++ 2M Karras, 30 steps, CFG 7.0 |

---

## Pipeline Workflows

### Workflow 1 — Character Identity Generation

Generate a consistent character across 6 poses (2x3 grid) with locked facial identity.

```
Load Checkpoint (Juggernaut XL v9)
  > IP-Adapter Unified Loader (FaceID Plus V2)
    > IPAdapter Apply (weight: 0.80, noise: 0.00)
      > ControlNet OpenPose (strength: 0.80)
        > CLIP Text Encode (positive + negative)
          > KSampler (30 steps, DPM++ 2M Karras, CFG 7.0, locked seed)
            > VAE Decode > Save Image
```

Seed-locking with pose-only variation ensures the same face renders identically across all 6 grid positions.

### Workflow 2 — Detector-Detailer Face Refinement

Automatically detect and refine facial details using segmentation-guided inpainting.

```
Load Image
  > UltralyticsDetectorProvider (face_yolov8m.pt)
    > BboxDetectorSEGS (threshold: 0.5, dilation: 10)
      > SAMLoader (sam_vit_b_01ec64.onnx, GPU)
        > SAMDetectorCombined (threshold: 0.93)
          > DetailerForEach (denoise: 0.40, feather: 5)
            > Save Image
```

ONNX-accelerated SAM generates pixel-perfect facial masks. DetailerForEach re-renders only within the mask at 0.40 denoise — sharpening features without altering identity.

### Workflow 3 — Multi-ControlNet Scene Composition

Place a consistent character into cinematic scenes with full environmental control.

```
Load Checkpoint (RealVisXL V4.0)
  > Load LoRA (cinematic style, weight: 0.65-0.75)
    > ControlNet Apply [1]: Depth (strength: 0.70)
      > ControlNet Apply [2]: Canny (strength: 0.50)
        > ControlNet Apply [3]: OpenPose (strength: 0.80)
          > KSampler (30 steps, DPM++ 2M Karras, CFG 7.0)
            > VAE Decode > Upscale (4x-UltraSharp) > Save Image
```

Three ControlNet layers separate spatial layout (Depth), edge structure (Canny), and character pose (OpenPose). LoRA at 0.65-0.75 adds cinematic color grading without overwhelming ControlNet signals.

---

## Environment and Hardware

| Spec | Detail |
|------|--------|
| GPU | NVIDIA RTX 3060 (12 GB VRAM) |
| RAM | 32 GB DDR4 |
| OS | Windows 11 |
| ComfyUI | Latest stable (portable build) |
| Python | 3.11+ with CUDA 12.x |
| Custom Nodes | Impact-Pack, IPAdapter-Plus, ControlNet-Aux, Ultralytics |

---

## Troubleshooting Expertise

| Issue | Resolution |
|-------|------------|
| Node not found after install | Flatten to custom_nodes/PackName/ |
| CUDA OOM on SDXL | Enable --lowvram flag or tile VAE decode |
| SAM ONNX won't load | pip install onnxruntime-gpu |
| DetailerForEach artifacts | Reduce denoise to 0.30-0.40; increase feather to 5-8 |
| IP-Adapter identity drift | Set weight >= 0.75, noise = 0.00 |
| ControlNet conflict | Lower secondary CN strengths; Depth primary |

---

## Project Structure

```
ai-creative-portfolio/
  README.md
  workflows/
    character-generation.md
    detector-detailer.md
    multi-controlnet-scene.md
  docs/
    comfyui-setup-guide.md
    demo-video-script.md
  samples/
    character-grid/
    detailer-before-after/
    cinematic-scenes/
```

---

## Contact

- Email: marcdoane0116@outlook.com
- Location: Pampa, TX (Remote)
- GitHub: armlancestronger

---

*This portfolio demonstrates end-to-end AI creative pipeline engineering — from character identity locking through cinematic scene composition, all built and optimized on consumer-grade hardware.*
