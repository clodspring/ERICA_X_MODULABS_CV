# VisA PCB4 이상 탐지 프로젝트

## 프로젝트 소개

제조업 비전 검사를 주제로 VisA PCB4 데이터셋을 이용해 정상 이미지 기반 이상 탐지를 수행하였다.

PaDiM을 중심으로 One-Class SVM, AutoEncoder를 비교하였으며 임계값과 특징 차원 변화가 이상 탐지 결과에 미치는 영향을 추가로 살펴보았다.

## 파일 링크
https://github.com/clodspring/ERICA_X_MODULABS_CV/blob/main/07_Anomaly_detection/VisA_PaDiM_PCB4_Submission.ipynb

## 사용 데이터

VisA PCB4

정상 이미지 1,005장, 불량 이미지 100장

정상 학습 분할 804장 중 실제 학습 200장

테스트 정상 201장, 불량 100장

## 실습 환경

Python 3.12.14

PyTorch 2.14.0

CUDA 사용

ResNet18 사전학습 Backbone

입력 이미지 192×192

## 실험 구성

먼저 PaDiM의 정상 특징 분포를 학습하고 이미지 단위 AUROC, AUPRC, F1과 혼동행렬을 계산하였다.

이후 정상 학습 점수의 90, 95, 97, 99, 99.5백분위를 임계값으로 사용하여 오탐과 미탐의 변화를 분석하였다.

특징 차원 32와 64를 비교해 탐지 성능 및 계산 시간을 확인하였다.

마지막으로 One-Class SVM과 AutoEncoder를 같은 PCB4 테스트 데이터에서 평가하였다.

## 주요 실행 결과

PaDiM AUROC: 0.8516

PaDiM AUPRC: 0.7092

PaDiM F1: 0.5946

One-Class SVM AUROC: 0.7806

AutoEncoder AUROC: 0.4588

특징 32차원 AUROC: 0.8350

특징 64차원 AUROC: 0.9002

위 수치는 저장된 노트북 실행 결과이며, 서로 다른 모델의 학습 조건이 완전히 동일하다는 의미는 아니다.

## 추가로 확인한 내용

임계값을 낮추면 불량 탐지율은 높아지지만 정상 제품에 대한 오탐도 증가하였다.

특징 차원 64에서 32보다 높은 AUROC가 나왔지만 계산 시간이 증가하였다. 차원별로 선택된 채널도 달라질 수 있으므로 차원 수만의 영향을 분석한 결과는 아니다.

AutoEncoder는 2 epoch만 학습한 상태이므로 충분히 학습된 모델과의 공정한 비교로 해석하기 어렵다.

## 노트북 실행

VisA_PaDiM_PCB4_Submission.ipynb 파일을 JupyterLab에서 열어 확인할 수 있다.

다시 실행하려면 기존 실습에서 사용한 VisA/pcb4 데이터셋과 Python 실행 환경이 필요하다.

코드에는 기존 로컬 작업 폴더의 경로가 포함되어 있으므로 다른 컴퓨터에서 실행할 때는 데이터 경로를 확인해야 한다.

## 결과 파일

실험 시 생성되는 padim_outputs 폴더에는 모델 평가표, 임계값 비교, 특징 차원 비교, ROC 그래프와 예측 히트맵 등이 저장된다.

원본 이미지 데이터셋과 특징 캐시 파일은 용량이 크므로 기본 제출 파일에서 제외하고, 실험 내용과 저장된 실행 결과가 포함된 노트북을 중심으로 제출한다.

## 한계 및 후속 계획

이번 실험은 정상 학습 이미지 200장을 사용하였고, AutoEncoder 학습도 2 epoch로 제한하였다.

향후 정상 학습 이미지 200장과 804장을 비교하고, 특징 차원별 반복 실험과 실제 결함 마스크를 활용한 Pixel-level 평가를 진행하고자 한다.

Gradio 인터페이스는 코드가 포함되어 있지만 이번 저장 실행에서는 비활성화되어 있다. 실제 웹 추론과 처리속도는 후속 검증이 필요하다.

자세한 실험 해석과 Keep, Problem, Try, Action After Review 회고는 제출용 노트북 마지막 부분에 정리하였다.

## 참고 자료

제조업 실시간 이상 탐지 시스템 구축 실습 강의자료

PaDiM: A Patch Distribution Modeling Framework for Anomaly Detection and Localization

https://arxiv.org/abs/2011.08785
