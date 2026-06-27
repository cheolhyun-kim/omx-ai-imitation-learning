# OMX-F 모방 학습 — ACT 기반 물건 집어 놓기

ROBOTIS OMX-F 로봇 팔에서 ACT(Action Chunking with Transformers)를 이용해 물건 집어 놓기(Pick and Place) 태스크를 수행하는 모방 학습 실험입니다.

---

## 목차

1. [프로젝트 개요](#1-프로젝트-개요)
2. [하드웨어 구성](#2-하드웨어-구성)
3. [데이터 수집](#3-데이터-수집)
4. [학습](#4-학습)
5. [실험 결과](#5-실험-결과)
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
| 데이터셋 | 100 에피소드, 약 45,000 프레임, 30fps, 카메라 1대 |
| HuggingFace 데이터셋 | [cheolhyunkim/omx_f_pick_and_place](https://huggingface.co/datasets/cheolhyunkim/omx_f_pick_and_place) |
| **최종 결과** | **100K 체크포인트에서 6/6 위치 성공 (100% 성공률)** |

저비용 6자유도 로봇 팔에서 사람이 직접 조작한 시연 데이터를 학습 신호로 삼아 엔드-투-엔드 모방 학습을 구현하는 것이 목표입니다.

---

## 2. 하드웨어 구성

- **로봇**: ROBOTIS OMX-F (6-DOF)
- **카메라**: RGB 카메라 1대, 30fps
- **학습 컴퓨터**: NVIDIA RTX 5060
- **미들웨어**: ROS2

---

## 3. 데이터 수집

| 항목 | 값 |
|---|---|
| 에피소드 수 | 100 |
| 총 프레임 수 | 약 45,000 |
| 프레임 레이트 | 30 fps |
| 카메라 수 | 1 |
| 수집 도구 | Physical AI Tools + LeRobot |

키네스테틱 티칭 / 원격 조작으로 에피소드를 수집했습니다. 각 에피소드는 목표 물체를 잡아 지정된 트레이에 올려놓는 단일 Pick-and-Place 사이클로 구성됩니다.

### 데이터 수집 영상

https://github.com/cheolhyun-kim/omx-f-imitation-learning/raw/main/assets/data_collection/data_collect.mp4

---

## 4. 학습

### 학습 Loss 그래프

![학습 Loss](assets/results/training_loss.png)

**분석:**
- **Total Loss**: 처음 5K step 이내에 급격히 감소하며 약 10K step 이후 안정화
- **L1 Loss** (행동 예측): 약 0.03 수준으로 수렴하며 소폭 변동
- **KLD Loss** (VAE 정규화): 5K step 이내에 거의 0으로 수렴
- KLD의 빠른 수렴은 VAE 인코더가 빠르게 압축된 잠재 표현을 학습했음을 시사

### 하이퍼파라미터

| 항목 | 값 |
|---|---|
| 정책 | ACT |
| 백본 | ResNet-18 |
| 배치 크기 | 32 |
| 학습 스텝 | 100,000 |
| 학습률 | 1e-5 |
| 학습 시간 | 약 12시간 |
| GPU | NVIDIA RTX 5060 |
| 체크포인트 저장 주기 | 20,000 step |

---

## 5. 실험 결과

![실험 그리드](assets/results/experiment_grid.jpg)

6개의 고정 위치에 물체를 놓고 체크포인트별로 Pick-and-Place 성공 여부를 테스트했습니다. 위치 인덱스는 아래와 같습니다.

```
로봇
 ↓
[3] [6]
[2] [5]
[1] [4]
```

Position 3이 로봇 베이스에서 가장 가까운 위치이며, Position 4, 5, 6은 로봇에서 먼 쪽입니다.

### 평가 방법

각 체크포인트를 작업 공간의 6개 고정 위치에 물체를 놓고 평가했습니다. 로봇은 물체를 집어 트레이에 놓는 동작을 시도합니다.

**범례:**
- ✅ = 첫 시도 성공
- ⚠️ = 조건부 성공 (재시도 필요 또는 불안정한 그리핑)
- ❌ = 실패

### 위치별 상세 결과

| 체크포인트 | Pos 1 | Pos 2 | Pos 3 | Pos 4 | Pos 5 | Pos 6 | 첫 시도 성공률 |
|---|---|---|---|---|---|---|---|
| 020k | ❌ | ✅ | ✅ | ✅ | ⚠️ 재시도 | ❌ | 3/6 (50%) |
| 040k | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ | 5/6 (83%) |
| 060k | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ | 5/6 (83%) |
| 080k | ⚠️ 재시도 | ✅ | ✅ | ⚠️ 불안정 | ✅ | ✅ | 4/6 (67%) |
| 100k | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | **6/6 (100%)** |

### 체크포인트별 추론 영상

#### 020k

https://github.com/cheolhyun-kim/omx-f-imitation-learning/raw/main/assets/inference/Inference_20000.mp4

#### 040k

https://github.com/cheolhyun-kim/omx-f-imitation-learning/raw/main/assets/inference/Inference_40000.mp4

#### 060k

https://github.com/cheolhyun-kim/omx-f-imitation-learning/raw/main/assets/inference/Inference_60000.mp4

#### 080k

https://github.com/cheolhyun-kim/omx-f-imitation-learning/raw/main/assets/inference/Inference_80000.mp4

#### 100k

https://github.com/cheolhyun-kim/omx-f-imitation-learning/raw/main/assets/inference/Inference_100000.mp4

---

## 6. 주요 관찰 사항

- **100K 체크포인트에서 6/6 완벽한 성공률 달성** — 충분한 학습 스텝이 모든 위치에서의 강건한 일반화에 핵심적임을 보여줌
- **Position 1이 가장 어려운 위치** — 020k, 040k, 060k에서 유일하게 실패했고, 080k에서야 처음 성공(재시도 필요). 해당 영역의 학습 데이터가 상대적으로 부족했을 가능성
- **040k와 060k는 동일한 성능 (83%)** — 중간 학습 구간에서 정책이 정체기에 도달한 후 100k에서 최종 도약
- **080k에서 일시적 성능 저하 발생** — Position 1에서 처음 성공했지만 Position 4에서 불안정한 그리핑이 발생하여 첫 시도 성공률이 67%로 040k/060k보다 낮아짐
- **비단조적 성능 향상 패턴 (50% → 83% → 83% → 67% → 100%)** — 학습 중 정책이 재구성되면서 일시적으로 안정성을 잃었다가 더 강건한 해로 수렴하는 과정을 시사
- 초기 체크포인트(020k~060k)의 성공 사례는 중앙 및 먼 영역(Position 2, 3, 4, 5)에 집중. 로봇 근처 영역(Position 1)은 가장 많은 학습이 필요

---

## 7. 문제 해결

| 문제 | 원인 | 해결 |
|---|---|---|
| DYNAMIXEL 모터 무응답 | 케이블 연결 불량으로 FastBulkRead 에러 발생 | Dynamixel Wizard 2.0으로 진단 후 모터 케이블 재연결 |
| v4l2_camera가 잘못된 카메라 캡처 | 리부팅 시 USB 장치 순서 변경, 내장 IR 카메라가 할당됨 | `/sys/class/video4linux/video*/name`으로 올바른 장치 확인 |
| LeRobot 버전 불일치 (Colab vs Docker) | 서로 다른 LeRobot 버전으로 인해 "mean is infinity" 추론 에러 | Colab 환경을 Physical AI Tools Docker 컨테이너와 동일한 LeRobot 버전으로 고정 |
| DroidCam "Connection reset" 에러 | v4l2loopback 커널 모듈 미로드 | `sudo modprobe v4l2loopback` 실행 |
| 학습 CSV 로그 미생성 | 소스 코드 수정 전에 이미 Python 모듈이 메모리에 로드됨 | 트레이너 소스 코드 수정 후 컨테이너 재시작, `os.makedirs()`로 출력 디렉토리 존재 보장 |

---

## 8. 참고 자료

- [ACT: Action Chunking with Transformers](https://tonyzhaozh.github.io/aloha/) — Zhao et al., 2023
- [LeRobot](https://github.com/huggingface/lerobot) — HuggingFace
- [Physical AI Tools](https://github.com/ROBOTIS-GIT/physical_ai_tools) — ROBOTIS
- [ROBOTIS OMX-F](https://www.robotis.com) — 로봇 하드웨어
- [ROS2](https://docs.ros.org)
- 데이터셋: [cheolhyunkim/omx_f_pick_and_place](https://huggingface.co/datasets/cheolhyunkim/omx_f_pick_and_place)
