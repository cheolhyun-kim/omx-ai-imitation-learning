# OMX-F Imitation Learning — Pick and Place with ACT

Imitation learning experiment on the ROBOTIS OMX-F robot arm using ACT (Action Chunking with Transformers) for a pick-and-place task.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Hardware Setup](#2-hardware-setup)
3. [Data Collection](#3-data-collection)
4. [Training](#4-training)
5. [Results](#5-results)
6. [Key Observations](#6-key-observations)
7. [Troubleshooting](#7-troubleshooting)
8. [References](#8-references)

---

## 1. Project Overview

| Item | Detail |
|---|---|
| Robot | OMX-F (ROBOTIS) |
| Task | Pick and Place |
| Policy | ACT (Action Chunking with Transformers) |
| Tools | Physical AI Tools, LeRobot, ROS2 |
| Dataset (HuggingFace) | [cheolhyunkim/omx_f_pick_and_place](https://huggingface.co/datasets/cheolhyunkim/omx_f_pick_and_place) |

The goal is to demonstrate end-to-end imitation learning on a low-cost 6-DOF robot arm using human teleoperation demonstrations as the training signal.

---

## 2. Hardware Setup

```
assets/setup/
├── robot_overview.jpg          # placeholder
├── workspace_layout.jpg        # placeholder
└── camera_mount.jpg            # placeholder
```

- **Robot**: ROBOTIS OMX-F (6-DOF)
- **Camera**: 1 × RGB camera, 30 fps
- **Compute (training)**: NVIDIA RTX 5060
- **Middleware**: ROS2

> Hardware assembly and calibration details to be added.

---

## 3. Data Collection

```
assets/data_collection/
├── teleop_demo.mp4             # placeholder
└── episode_sample.png          # placeholder
```

| Property | Value |
|---|---|
| Episodes | 100 |
| Total frames | ~45,000 |
| Frame rate | 30 fps |
| Cameras | 1 |
| Collection tool | Physical AI Tools + LeRobot |

Episodes were collected via kinesthetic teaching / teleoperation. Each episode covers a single pick-and-place cycle: grasp the target object and place it at the designated location.

---

## 4. Training

```
configs/
└── act_omxf_pick_place.yaml    # placeholder
```

| Hyperparameter | Value |
|---|---|
| Policy | ACT |
| Backbone | ResNet-18 |
| Batch size | 32 |
| Steps | 100,000 |
| Learning rate | 1e-5 |
| Training time | ~12 hrs |
| Hardware | RTX 5060 |

Checkpoints were saved at: `020k`, `040k`, `060k`, `080k`, `100k`.

---

## 5. Results

Each checkpoint was evaluated over **6 attempts**.

```
assets/results/
├── checkpoint_comparison.png   # placeholder
├── success_rate_curve.png      # placeholder
└── eval_videos/
    ├── ckpt_020k.mp4           # placeholder
    ├── ckpt_040k.mp4           # placeholder
    ├── ckpt_060k.mp4           # placeholder
    ├── ckpt_080k.mp4           # placeholder
    └── ckpt_100k.mp4           # placeholder
```

| Checkpoint | Attempts | Successes | Success Rate |
|---|---|---|---|
| 020k | 6 | — | —% |
| 040k | 6 | — | —% |
| 060k | 6 | — | —% |
| 080k | 6 | — | —% |
| 100k | 6 | — | —% |

> Results to be filled in after evaluation runs.

---

## 6. Key Observations

> To be updated after experiments.

- [ ] Which checkpoint achieved the best generalization?
- [ ] Did the policy overfit at 100k steps?
- [ ] Common failure modes observed during inference.

```
assets/inference/
├── success_example.mp4         # placeholder
└── failure_example.mp4         # placeholder
```

---

## 7. Troubleshooting

> To be updated with issues encountered during setup, data collection, and training.

| Issue | Cause | Fix |
|---|---|---|
| — | — | — |

---

## 8. References

- [ACT: Action Chunking with Transformers](https://tonyzhaozh.github.io/aloha/) — Zhao et al., 2023
- [LeRobot](https://github.com/huggingface/lerobot) — HuggingFace
- [Physical AI Tools](https://github.com/ROBOTIS-GIT/physical_ai_tools) — ROBOTIS
- [ROBOTIS OMX-F](https://www.robotis.com) — Robot hardware
- [ROS2](https://docs.ros.org)
- Dataset: [cheolhyunkim/omx_f_pick_and_place](https://huggingface.co/datasets/cheolhyunkim/omx_f_pick_and_place)
