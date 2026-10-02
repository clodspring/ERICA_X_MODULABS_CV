# EfficientDet 기반 제조 영상 분석 최적화

## 프로젝트 개요

이번 프로젝트에서는 EfficientDet-D0의 구조와 추론 과정을 확인하고 Custom Dataset을 이용해 직접 학습과 평가를 진행했습니다.

교안의 기본 실습에서 더 나아가 데이터셋의 특징을 직접 분석하고 실제 영상에서 EfficientDet의 처리속도를 측정했습니다.

또 이전 실습에서 사용한 YOLOv8n을 동일한 영상과 PC 환경에서 실행해 Model FPS, End-to-End FPS, P95 Latency를 비교했습니다.

단순히 어떤 모델이 더 좋다고 판단하기보다는 모델의 구조와 실제 실행 환경에 따라 어떤 차이가 나타나는지 직접 확인하는 것을 목표로 진행했습니다.

## 실행 Notebook

[EfficientDet 프로젝트 Notebook 보기](./EfficientDet_submission_final.ipynb)

## 주요 작업

- COCO 형식의 차량 Dataset 구조를 확인했습니다.
- Train, Validation, Test 데이터의 이미지와 Annotation 수를 확인했습니다.
- Train 이미지에서 RGB Mean과 Standard Deviation을 직접 계산했습니다.
- Bounding Box 분포를 이용해 Anchor Ratio와 Anchor Scale을 확인했습니다.
- EfficientDet 학습에 맞게 COCO Dataset 구조와 Category ID를 변환했습니다.
- EfficientDet-D0 Detection Head를 먼저 학습했습니다.
- Head 학습 결과를 이용해 전체 모델 Fine Tuning을 진행했습니다.
- COCO Evaluation을 이용해 mAP와 객체 크기별 AP를 확인했습니다.
- 실제 Low Quality와 High Quality 영상에서 EfficientDet 추론을 진행했습니다.
- 같은 영상에서 YOLOv8n을 실행해 처리속도를 비교했습니다.
- Model FPS와 실제 영상 처리속도인 End-to-End FPS의 차이를 분석했습니다.
- P95 Latency를 이용해 평균값만으로 확인하기 어려운 느린 추론 구간도 확인했습니다.

## Dataset 분석

학습 데이터는 Train 102장, Validation 28장, Test 13장으로 구성되어 있었고 Train Dataset에는 2069개의 Annotation이 포함되어 있었습니다.

교안의 기본 설정을 그대로 사용하는 것보다 현재 Dataset의 특성을 먼저 확인해보는 것이 좋다고 생각했습니다.

Train 이미지에서 계산한 RGB Mean은 약 `[0.464, 0.473, 0.470]`, Standard Deviation은 약 `[0.165, 0.164, 0.163]`으로 확인되었습니다.

Bounding Box를 분석한 결과 Anchor Ratio 후보는 약 `[0.52, 0.67, 0.82]`, 정규화한 Anchor Scale은 약 `[0.54, 1.00, 2.13]`으로 확인되었습니다.

이 값들이 기본 설정보다 항상 좋은 성능을 낸다고 결론내리기보다는 데이터에 맞는 전처리와 Anchor 설정을 직접 확인해보는 과정으로 진행했습니다.

## 학습 과정

### 1단계 Detection Head 학습

먼저 사전학습된 EfficientDet-D0를 이용해 Detection Head를 10 Epoch 학습했습니다.

기존에 학습된 특징은 최대한 유지하면서 현재 차량 Dataset의 클래스와 Bounding Box 예측 부분을 먼저 맞추는 과정으로 이해했습니다.

### 2단계 전체 모델 Fine Tuning

Head 학습에서 생성된 Checkpoint를 이용해 `head_only=False`로 전체 모델을 추가 학습했습니다.

최종 Checkpoint를 이용해 Validation Dataset에서 COCO Evaluation을 진행했습니다.

## COCO 평가 결과

| 항목 | 결과 |
|---|---:|
| mAP@0.5:0.95 | 약 0.123 |
| mAP@0.5 | 약 0.252 |
| mAP@0.75 | 약 0.082 |
| AP Small | 약 0.083 |
| AP Medium | 약 0.234 |
| AP Large | 약 0.271 |
| AR@100 | 약 0.268 |

현재 결과에서는 Large와 Medium 객체보다 Small 객체의 AP가 낮게 나타났습니다.

따라서 전체 mAP 하나만 확인하기보다 객체 크기에 따른 성능 차이도 함께 확인하는 것이 필요하다고 생각했습니다.

## 추가 실험 1 - 실제 영상 추론

이미지 Test에서 끝내지 않고 제공된 Low Quality와 High Quality 영상에 EfficientDet-D0를 적용했습니다.

평균 Inference Time뿐 아니라 P95 Latency, Model FPS, End-to-End FPS를 함께 측정했습니다.

## 추가 실험 2 - YOLOv8n과 처리속도 비교

| Model | Video | Inference ms | P95 ms | Model FPS | End-to-End FPS |
|---|---|---:|---:|---:|---:|
| EfficientDet-D0 | Low Quality | 155.63 | 304.14 | 6.43 | 1.99 |
| EfficientDet-D0 | High Quality | 141.50 | 283.64 | 7.07 | 2.16 |
| YOLOv8n | Low Quality | 30.36 | 73.34 | 32.93 | 9.74 |
| YOLOv8n | High Quality | 39.16 | 94.70 | 25.53 | 7.58 |

이번 환경에서는 두 영상 모두 YOLOv8n이 EfficientDet-D0보다 높은 Model FPS와 End-to-End FPS를 보였습니다.

다만 이 결과만으로 YOLO가 모든 조건에서 EfficientDet보다 좋은 모델이라고 판단할 수는 없습니다.

모델 구조뿐 아니라 구현 방식, Framework, GPU 최적화와 사용한 하드웨어 환경도 실제 처리속도에 영향을 줄 수 있기 때문입니다.

## Model FPS와 End-to-End FPS 비교

실험 전에는 Model FPS가 실제 영상 처리속도와 거의 비슷할 것이라고 생각했습니다.

하지만 EfficientDet과 YOLO 모두 End-to-End FPS가 Model FPS보다 크게 낮게 나타났습니다.

영상 읽기, 전처리, 모델 추론, Bounding Box 후처리, 결과 영상 생성과 저장까지 전체 과정이 추가되기 때문이라고 생각했습니다.

따라서 실제 제조 영상 시스템의 실시간 처리 가능 여부를 판단할 때는 모델 자체 FPS뿐 아니라 전체 Pipeline의 End-to-End FPS를 같이 확인해야 한다는 점을 알게 되었습니다.

## 비교 실험의 한계

이번 EfficientDet과 YOLO 비교는 동일한 영상과 동일한 PC 환경에서 진행했기 때문에 처리속도의 차이를 확인하는 데 의미가 있습니다.

하지만 두 모델이 동일한 Dataset과 클래스 조건으로 학습된 것은 아니기 때문에 탐지 개수나 평균 Confidence를 이용해 정확도의 우열을 직접 판단하지 않았습니다.

정확도를 공정하게 비교하려면 동일한 Train Dataset과 동일한 Ground Truth를 이용해 두 모델의 mAP, Precision, Recall을 다시 평가해야 한다고 생각했습니다.

또 Low Quality와 High Quality 영상의 처리속도에 차이가 나타났지만 영상 품질 자체가 그 차이의 원인이라고 단정하지 않았습니다.

영상 내용, 객체 수, 압축 방식과 GPU 상태 등 다른 조건도 영향을 줄 수 있기 때문입니다.

## AI 도구 활용

Python 코드 작성과 환경 설정, 오류를 해결하는 과정에서 ChatGPT를 보조 도구로 활용했습니다.

생성된 코드를 그대로 실행하기보다는 각 코드의 역할을 확인하고 직접 실행한 결과를 비교하면서 실험 조건과 결과를 정리했습니다.

특히 데이터 특성 분석, 영상 추론, YOLOv8n 비교와 추가 성능 분석은 교안의 기본 실습에서 확장하여 진행했습니다.

## KPT

### Keep

교안의 이미지 추론에서 끝내지 않고 실제 영상에서 모델이 어느 정도 속도로 동작하는지 확인한 과정이 도움이 되었습니다.

Model FPS뿐 아니라 P95 Latency와 End-to-End FPS까지 같이 측정한 방식도 앞으로 유지하고 싶습니다.

### Problem

EfficientDet은 YOLO보다 직접 설정해야 하는 부분이 많았고 오픈소스 Repository의 구조와 COCO Dataset 형식을 이해하는 과정에서 시간이 많이 필요했습니다.

또 처음에는 탐지 개수와 Confidence를 이용해 두 모델의 정확도를 바로 비교하려고 했지만 학습 조건이 다르면 공정한 비교가 어렵다는 점을 알게 되었습니다.

### Try

다음에는 EfficientDet과 YOLO를 동일한 Dataset과 클래스 조건으로 학습한 뒤 mAP와 FPS를 함께 비교해보고 싶습니다.

또 EfficientDet-D0뿐 아니라 D1처럼 Compound Scale을 변경했을 때 정확도와 처리속도가 어떻게 달라지는지도 확인해보고 싶습니다.

### Action After Review

앞으로 객체 탐지 모델을 비교할 때 Inference Time뿐 아니라 End-to-End FPS와 P95 Latency도 같이 확인하려고 합니다.

또 서로 다른 모델의 결과를 비교하기 전에 학습 데이터와 평가 조건이 동일한지 먼저 확인하고 결과를 해석하려고 합니다.

## Repository 구성

```text
05_EfficientDet/
├── README.md
├── EfficientDet_submission_final.ipynb
├── requirements_submission.txt
├── .gitignore
└── analysis_outputs/
    ├── video_model_comparison.csv
    └── video_speed_summary.csv
```

## Reference

수업자료 `EfficientDet 기반 제조 영상 분석 최적화`

Yet-Another-EfficientDet-Pytorch

https://github.com/zylo117/Yet-Another-EfficientDet-Pytorch
