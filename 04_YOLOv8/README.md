# YOLOv8 기반 제조 데이터 객체 탐지 실습

## 프로젝트 개요

이번 프로젝트에서는 YOLOv8n을 이용해 shoes와 stamp 두 종류의 객체를 직접 학습하고 탐지 결과를 확인했습니다

수업 교안에서 진행한 데이터 준비, YOLOv8 학습, Validation 평가와 Test 이미지 예측을 먼저 진행했습니다

이후 기본 실습에서 끝내지 않고 데이터셋의 특징과 모델의 성능 변화를 조금 더 확인해보기 위해 추가 실험을 진행했습니다

## 주요 작업

- YOLO 형식 데이터셋과 YAML 파일을 확인했습니다
- Train, Validation, Test 데이터의 구성을 확인했습니다
- 클래스별 객체 수와 Bounding Box 크기를 분석했습니다
- Pretrained YOLOv8n을 이용해 Fine Tuning을 진행했습니다
- Precision, Recall, mAP 결과를 확인했습니다
- shoes와 stamp의 클래스별 성능을 비교했습니다
- Test 이미지에서 실제 객체 탐지 결과를 확인했습니다
- 입력 이미지 크기에 따라 mAP와 추론 속도가 어떻게 달라지는지 비교했습니다
- 모델이 상대적으로 낮은 Confidence를 보인 이미지도 따로 확인했습니다
- 마지막으로 KPT 방식으로 실습 과정을 정리했습니다

## 기본 학습 조건

| 항목 | 설정 |
|---|---|
| Model | YOLOv8n |
| Epoch | 20 |
| Image size | 640 |
| Batch size | 16 |
| Device | NVIDIA GPU |
| Dataset | Roboflow stamp-bcrhe |

## Validation 결과

| 항목 | 결과 |
|---|---:|
| Precision | 약 0.94 |
| Recall | 약 0.99 |
| mAP@0.5 | 약 0.987 |
| mAP@0.5:0.95 | 약 0.639 |
| shoes mAP@0.5:0.95 | 약 0.721 |
| stamp mAP@0.5:0.95 | 약 0.557 |

전체 Recall과 mAP@0.5는 높게 나왔습니다

하지만 더 엄격한 IoU 범위를 사용하는 mAP@0.5:0.95에서는 값이 낮아졌고 shoes와 stamp 사이에서도 클래스별 성능 차이가 나타났습니다

그래서 전체 mAP 하나만 확인하기보다는 클래스별 결과와 실제 예측 이미지도 같이 확인했습니다

## 추가 실험

### 데이터 특성 확인

클래스별 객체 수와 Bounding Box 크기를 확인해서 학습 데이터 자체의 특징을 살펴봤습니다

이 결과를 클래스별 mAP 차이와 함께 보면서 데이터 구성도 모델 성능에 영향을 줄 수 있는지 확인했습니다

### 입력 이미지 크기 비교

416, 640, 832 세 가지 입력 크기에서 동일한 모델을 평가했습니다

각 조건에서 mAP와 inference time을 함께 확인해서 정확도와 처리 속도 사이의 차이를 비교했습니다

실제 제조 현장에서는 가장 높은 정확도만 선택하기보다 필요한 처리 속도와 탐지 성능을 같이 고려해야 한다고 생각했습니다

### 예측 사례 확인

Test 이미지 중에서 상대적으로 낮은 Confidence가 나온 이미지를 따로 확인했습니다

평균 성능만으로는 알기 어려운 모델의 판단 특성을 실제 이미지로 확인해보는 것이 목적이었습니다

## AI 도구 활용

Python 코드 작성과 오류를 해결하는 과정에서 ChatGPT를 보조 도구로 활용했습니다

생성된 코드를 그대로 사용하는 것보다는 각 코드가 어떤 기능을 하는지 확인하고 실제 결과를 보면서 조건을 바꾸거나 결과를 해석하려고 했습니다

## KPT

### Keep

mAP만 확인하는 것보다 실제 예측 이미지와 클래스별 결과, 추론 속도를 같이 확인하는 것이 모델을 이해하는 데 더 도움이 되었습니다

앞으로도 한 가지 결과만 보는 것보다 여러 결과를 같이 비교하는 방식을 유지하고 싶습니다

### Problem

초기에는 CUDA 환경과 데이터 경로를 설정하는 과정에서 여러 오류가 발생했습니다

또 전체 성능만 보면 확인하기 어려운 클래스별 성능 차이가 있다는 것도 알게 되었습니다

### Try

다음에는 데이터 증강 조건이나 다른 크기의 YOLO 모델도 같은 데이터에서 비교해보고 싶습니다

또 클래스별 객체 수와 크기가 결과에 어떤 영향을 주는지도 더 자세하게 확인해보고 싶습니다

### Action After Review

앞으로 객체 탐지 모델을 평가할 때 전체 mAP뿐만 아니라 클래스별 결과, 실제 예측 이미지, 추론 속도와 입력 조건에 따른 결과 변화도 함께 확인하려고 합니다

## Repository 구성

```text
04_YOLOv8
├── README.md
├── YOLOv8_stamp_submission.ipynb
├── requirements.txt
├── .gitignore
└── analysis_outputs
    └── imgsz_comparison.csv
```

## Dataset

Roboflow stamp-bcrhe dataset

https://universe.roboflow.com/warisara-kaewsuwan-cf2hs/stamp-bcrhe

## Reference

Ultralytics YOLOv8

수업 제공 YOLOv8 기반 제조 데이터 객체 탐지 실습 자료
