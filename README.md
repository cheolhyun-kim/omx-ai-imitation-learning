# OMX-F 모방 학습 — ACT 기반 물건 집어 놓기

ROBOTIS OMX-F 로봇 팔에서 ACT(Action Chunking with Transformers)를 이용해 물건 집어 놓기(Pick and Place) 태스크를 수행하는 모방 학습 실험입니다.

---

## 목차

1. [프로젝트 개요](#1-프로젝트-개요)
2. [하드웨어 구성](#2-하드웨어-구성)
3. [데이터 수집](#3-데이터-수집)
4. [학습](#4-학습)
5. [결과](#5-결과)
6. [주요 관찰 사항](#6-주요-관찰-사항)
7. [문제 해결](#7-문제-해결)
8. [참고 자료](#8-참고-자료)

---

## 1. 프로젝트 개요

| 항목 | 내용 |
|---|---|
| 로봇 | OMX-F (ROBOTIS) |
| 태스크 | Pick and Place |
| 정책 | ACT (Action Chunking with Transformers) |
| 도구 | Physical AI Tools, LeRobot, ROS2 |
| 데이터셋 (HuggingFace) | [cheolhyunkim/omx_f_pick_and_place](https://huggingface.co/datasets/cheolhyunkim/omx_f_pick_and_place) |

저비용 6자유도 로봇 팔에서 사람이 직접 조작한 시연 데이터를 학습 신호로 삼아 엔드-투-엔드 모방 학습을 구현하는 것이 목표입니다.

---

## 2. 하드웨어 구성

```
assets/setup/
├── robot_overview.jpg          # 추가 예정
├── workspace_layout.jpg        # 추가 예정
└── camera_mount.jpg            # 추가 예정
```

- **로봇**: ROBOTIS OMX-F (6-DOF)
- **카메라**: RGB 카메라 1대, 30fps
- **학습 컴퓨터**: NVIDIA RTX 5060
- **미들웨어**: ROS2

> 하드웨어 조립 및 캘리브레이션 상세 내용은 추후 추가 예정입니다.

---

## 3. 데이터 수집

```
assets/data_collection/
├── teleop_demo.mp4             # 추가 예정
└── episode_sample.png          # 추가 예정
```

| 항목 | 값 |
|---|---|
| 에피소드 수 | 100 |
| 총 프레임 수 | 약 45,000 |
| 프레임 레이트 | 30 fps |
| 카메라 수 | 1 |
| 수집 도구 | Physical AI Tools + LeRobot |

키네스테틱 티칭 / 원격 조작으로 에피소드를 수집했습니다. 각 에피소드는 목표 물체를 잡아 지정된 위치에 올려놓는 단일 Pick-and-Place 사이클로 구성됩니다.

---

## 4. 학습

```
configs/
└── act_omxf_pick_place.yaml    # 추가 예정
```

| 하이퍼파라미터 | 값 |
|---|---|
| 정책 | ACT |
| 백본 | ResNet-18 |
| 배치 크기 | 32 |
| 스텝 수 | 100,000 |
| 학습률 | 1e-5 |
| 학습 시간 | 약 12시간 |
| 하드웨어 | RTX 5060 |

체크포인트 저장 시점: `020k`, `040k`, `060k`, `080k`, `100k`

---

## 5. 결과

각 체크포인트별 **6회** 평가를 진행했습니다.

```
assets/results/
├── training_loss.png
├── checkpoint_comparison.png   # 추가 예정
├── success_rate_curve.png      # 추가 예정
└── eval_videos/
    ├── ckpt_020k.mp4           # 추가 예정
    ├── ckpt_040k.mp4           # 추가 예정
    ├── ckpt_060k.mp4           # 추가 예정
    ├── ckpt_080k.mp4           # 추가 예정
    └── ckpt_100k.mp4           # 추가 예정
```

| 체크포인트 | 시도 횟수 | 성공 횟수 | 성공률 |
|---|---|---|---|
| 020k | 6 | — | —% |
| 040k | 6 | — | —% |
| 060k | 6 | — | —% |
| 080k | 6 | — | —% |
| 100k | 6 | — | —% |

> 평가 실행 후 결과를 채울 예정입니다.

---

## 6. 주요 관찰 사항

> 실험 후 업데이트 예정입니다.

- [ ] 어느 체크포인트에서 일반화 성능이 가장 좋았는가?
- [ ] 100k 스텝에서 과적합이 발생했는가?
- [ ] 추론 중 자주 관찰된 실패 패턴은 무엇인가?

```
assets/inference/
├── success_example.mp4         # 추가 예정
└── failure_example.mp4         # 추가 예정
```

---

## 7. 문제 해결

> 설정, 데이터 수집, 학습 중 발생한 문제를 추후 기록할 예정입니다.

| 문제 | 원인 | 해결 방법 |
|---|---|---|
| — | — | — |

---

## 8. 참고 자료

- [ACT: Action Chunking with Transformers](https://tonyzhaozh.github.io/aloha/) — Zhao et al., 2023
- [LeRobot](https://github.com/huggingface/lerobot) — HuggingFace
- [Physical AI Tools](https://github.com/ROBOTIS-GIT/physical_ai_tools) — ROBOTIS
- [ROBOTIS OMX-F](https://www.robotis.com) — 로봇 하드웨어
- [ROS2](https://docs.ros.org)
- 데이터셋: [cheolhyunkim/omx_f_pick_and_place](https://huggingface.co/datasets/cheolhyunkim/omx_f_pick_and_place)
