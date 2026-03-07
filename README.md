# 📈 무역 품목 간 공행성 쌍 판별 및 무역량 예측 AI 모델

**[DACON] 제3회 국민대학교 AI빅데이터 분석 경진대회**
<img width="1319" height="290" alt="image" src="https://github.com/user-attachments/assets/b0f12304-5dd1-4418-9504-08e9279fa999" />

---

 ### DACON 4등 - 우수상

<div align="center">
 <img width="800" height="480" alt="image" src="https://github.com/user-attachments/assets/426b96b6-7bc0-4956-b614-79fd85666c67" />
</div>

---

# **CONTENTS**

* **프로젝트 개요**

* **데이터 개요 및 탐색적 데이터 분석(EDA)**

* **핵심 알고리즘 : 공행성 쌍 탐색 및 전처리**

* **예선 & 본석 모델 설명**

* **대회를 통한 느낀점** 

--- 

# 프로젝트 개요

* **대회명** : 제 3회 국민대학교 AI빅데이터 분석 경진대회

* **주제** : 무역 품목 간 공행성 쌍 판별 및 후행 품목 무역량 예측 AI 모델 개발

* **주최/주관** : 국민대학교 경영대학원, 한국기계산업진흥회(KOAMI) 공동 주최/데이콘 운영

* **팀명** : 헬퍼_장광남 (유대영)

* **목표** : 100개 수입 품목의 월별 무역 데이터를 분석하여 품목 간 선후행 관계(공행성)를 발굴하고, 이를 기반으로 익월 무역량(Value)을 예측하는 알고리즘 개발

---

# 데이터 개요 및 탐색적 데이터 분석 (EDA) 

## 1️⃣ 데이터 개요 

<div align="center">
  <img src="https://github.com/user-attachments/assets/8357b146-5e64-4019-bd13-637504c90876" alt="이미지 1" width="32%" height="300" style="object-fit: cover; margin-right: 1%;">
  <img src="https://github.com/user-attachments/assets/9cb1ea44-3631-4b69-96e4-9f0feecd9752" alt="이미지 2" width="32%" height="300" style="object-fit: cover; margin-right: 1%;">
  <img src="https://github.com/user-attachments/assets/bd12fb70-4808-421c-879a-217a4d038bed" alt="이미지 3" width="32%" height="300" style="object-fit: cover; margin-right: 1%;">
</div>

* **예선 데이터** : 총 10,836건 (2022년 1월 ~ 2025년 7월 데이터)

* **본선 데이터** : 총 11,356건 (2025년 9월까지의 확장), 최신 경향 반영

* **주요 특성** : item_id(품목 식별자), value(금액), weight(중량), hs4(분류 코드), seq(차수) 등 포함

🔍 100개 품목은 익명화

🔍 단순 value만 보고 접근하기 어려움
   
&emsp;&emsp;➡️ type은 모두 1, quantity는 대부분 0으로 비어있는 정보, weight은 데이터가 충실한 편 ➡️ weight & value를 동시 분석하는 것이 중요

🔍 value & weight : 최솟값과 최대값의 차이가 극심 

## 2️⃣ 초기 분석 인사이트 (EDA) 

### 💡 컬럼 별 관계분석

* Value와 Weight의 동시분석이 가장중요
<div align="center">
<img width="600" height="480" alt="image" src="https://github.com/user-attachments/assets/b5eae740-fb6a-46d1-bb2a-edfd3b9c4682" />
</div>

### 💡 금액 분포

* 거래 금액의 편차가 극심함을 발견

* Log 변환을 통한 정규화 진행 (필수)
<div align="center">
<img width="1591" height="533" alt="image" src="https://github.com/user-attachments/assets/59b9c975-fb4c-42ad-ab29-2d84dfb1c2d7" />
</div>

### 💡 무게 분포

* 무계 또한 편차가 극심함을 발견

* Log 변환을 통한 정규화 진행 (필수)
<div align="center">
<img width="1593" height="536" alt="image" src="https://github.com/user-attachments/assets/c2d63084-5118-4acb-9023-ad529bd3d469" />
</div>

### 💡 무게와 금액의 관계 

* 양의 상관관계 파악 가능

* 완벽한 1의 관계는 아니지만, 어느정도의 고수준 확인

* 퍼짐을 통해 단가가 있음을 시사

* 단순 관계 판단에 있어서 Weight = value는 아니지만 중요한 관계임을 파악 가능
<div align="center">
<img width="600" height="480" alt="image" src="https://github.com/user-attachments/assets/262b3bc6-9149-40aa-b12e-7848c3b45551" />
</div>

### 💡 차수 분포 

| 년도별 차수 분포 | 달별 차수 분포 |
|---------|---------|
| <img src="https://github.com/user-attachments/assets/29392b01-50c5-47a4-97a8-1bace85a6558" width="550" height="420" /> | <img src="https://github.com/user-attachments/assets/486129a5-6561-475c-b883-e682b3d7358c" width="550" height="420" /> |

### 💡 금액 분포

| 년도별 금액 분포 | 달별 금액 분포 |
|---------|---------|
| <img src="https://github.com/user-attachments/assets/392838e1-eba1-4a51-9dc7-45c99d79d942" width="550" height="420" /> | <img src="https://github.com/user-attachments/assets/3ad246ec-9272-45c9-84ee-95343c686535" width="550" height="420" /> |

### 💡 시계열의 희소성

* 특정 품목은 거래가 끊기는 구간이 존재

* 데이터가 없는 구간의 예측 정확도를 높이기 위해 다른 품목의 과거 데이터를 참조하는 '공행성 기반 접근'의 당위성 확인.

<img width="1276" height="447" alt="image" src="https://github.com/user-attachments/assets/3800705c-22d9-4e3a-b7c3-88785dbceef3" />

--- 

# 핵심 알고리즘 : 공행성 쌍 탐색 및 전처리 (예선 & 본선)

## 1️⃣ 시계열 Pivot Table 변환 

* 개별 품목 중심의 구조에서 품목 간 상관관계(Pair)를 파악할 수 있는 구조로 확장하기 위해 월별 피벗 테이블 생성
  
* **예선전** :  value, weight, hs4

* **본선전** : value, weight, hs4, cnt

| 예선전 전처리 | 본선전 천처리 |
|---------|---------|
| <img src="https://github.com/user-attachments/assets/d94d44c7-115a-4acd-b2c8-6342285e0e55" width="550" height="420" /> | <img src="https://github.com/user-attachments/assets/61985e3e-6cbf-4e7b-9c11-e5e74d73b08c" width="550" height="420" /> |

## 2️⃣공행성 쌍(Pair) 추출 알고리즘의 고도화

**예선전**

* 단순 상관관계 위주로 두 품목 간의 피어슨 상관계수가 0.35 이상인 쌍을 찾고, 후행 품목당 상위 3개의 선행 품목만 단순 매칭 시도
 
<div align="center">
  <img src="https://github.com/user-attachments/assets/ac3f8627-5ad8-4f8a-bf33-15061a5c26d1" alt="예선전 이미지 1" width="48%" style="margin-right: 1%;">
  <img src="https://github.com/user-attachments/assets/6b00126a-be47-4c4d-9b8a-e1f720f6bd43" alt="예선전 이미지 2" width="48%">
</div>

**본선전** 

* 상관관계 + 인과관계 하이브리드 검증으로 우연히 비슷하게 움직이는 노이즈 제거를 위해 그랜저 인간관계 검정 도입
  
* 상관계수 * 데이터수 제곱근 * 인과성 가산점(bonus)을 결합한 자체 Score 체계를 구축하여 신뢰도 높은 페어만 추출

<div align="center">
  <img src="https://github.com/user-attachments/assets/e1d7e724-9d9f-4df3-a6eb-6c5e1c273378" alt="본선전 이미지 1" height="250" style="margin-right: 10px;">
  <img src="https://github.com/user-attachments/assets/f10628d5-0203-4cfc-8d8e-1facc031e9cc" alt="본선전 이미지 2" height="250" style="margin-left: 10px;">
</div>

## 3️⃣파생 변수(Feature Engineering)의 심화 및 확장

**예선전**

* 과거 값(lag), 전월 대비 변화량(delta), 3/6개월 이동평균(MA) 등 기초적인 시계열 피처 생성

* **본선전**

* 개별 품목 중심(11,356행 x 9열)의 원본 데이터를 선행-후행 쌍 중심(47,897행 x 44열)으로 폭발적으로 확장

* **주기성** : 월(Month) 데이터를 sin, cos 함수로 변환하여 계절성을 딥러닝이 인식하기 쉽게 처리

* **변동성 및 모멘텀** : 6개월 표준편차(b_std6), 지수이동평균(bema), 전년 동기 대비 변화율(yoy_change), 연속 증가 횟수 등 고차원 통계 지표 추가

---

# 예선전 & 본선전 모델 설명

## 사용 알고리즘

**⭐ 예선전 (LightGBM)**

**장점**

* **압도적인 효율성** : 정형 데이터 처리에 특화되어 있어 학습 및 추론 속도가 매우 빠르고 연산 자원 소모가 적음

* **안정성** : 결측치나 이상치에 상대적으로 강건하며, Optuna를 활용한 하이퍼파라미터 튜닝이 직관적이고 빠르게 수렴

**단점** 

* **복잡한 패턴 인식 한계** : 트리 모델 특성상, 수많은 변수들이 서로 복잡하게 얽혀서 만들어내는 상호작용이나 숨겨진 비선형적 관계를 깊게 파악하는 데에는 구조적 한계가 존재

**⭐ 본선전 (FT-Transformer + LightGBM 하이브리드 모델)**

**장점** 

* **상호 보완적 시너지 창출** :  딥러닝의 '어텐션 메커니즘을 통한 복잡한 변수 간 관계 파악 능력'과 머신러닝의 '계층적 패턴 추출 능력'을 결합하여 예측의 사각지대를 최소화

* **정형 데이터의 토큰화** : 각 컬럼을 독립적인 토큰으로 취급하여, 기존 머신러닝이 잡아내지 못하는 변수 간의 문맥적 의미를 학습

* **예측 분산 감소 (3-Seed Ensemble)** : 딥러닝은 초기 가중치에 따라 결과가 달라지는 불안정성이 있음. 3개의 다른 시드로 학습 후 평균을 내어 모델의 일반화 성능을 극대화하고 과적합 방지

* **스마트 필터링** : 전처리 단계에서 정의한 '쌍의 신뢰도'를 척도로 사용하여, 애초에 근거가 부족한 데이터(하위 5%)의 예측을 배제함으로써 전체 평가 지표가 무너지는 것을 방어

**단점** 

* **리소스 및 시간 소모** : 딥러닝 모델의 도입으로 인해 학습 시간 및 추론 시간이 단일 모델 대비 기하급수적으로 증가

* **전처리 의존도 상승** : 딥러닝은 데이터 스케일링에 매우 민감하므로(Quantile Transformer 필수), 데이터 파이프라인이 복잡

* **스마트 필터링 대체 로직 필요** : 하위 5%의 예측값을 제외한 후, 해당 품목들에 대해 어떤 값으로 결측을 채워 넣을지에 대한 확실한 Fallback 로직이 추가 요구구

---

# 대회를 통한 회고 및 배운 점
본 대회를 진행하며 가장 크게 체감한 바는 데이터의 특성 및 크기와 모델 아키텍처 간의 적합성이 예측 성능을 좌우하는 핵심 요소라는 점임. 본선에서는 파생 변수를 44개로 대폭 확장하고, FT-Transformer와 LightGBM의 하이브리드 앙상블, 그랜저 인과관계 기반의 스마트 필터링 등 알고리즘을 고도화를 진행했으나 제한된 크기의 무역 정형 데이터 환경에서는 복잡한 딥러닝 연산보다 기본 피처 중심의 단일 트리 모델(LightGBM)이 오히려 직관적이고 안정적인 패턴을 포착하는 데 유리함을 확인함. 딥러닝은 변수 간의 복잡한 상호작용을 학습하는 데 탁월하지만 이를 뒷받침할 압도적인 데이터 양이 필수적이며, 무리한 피처 확장과 엄격한 예측값 필터링 적용은 모델이 학습해야 할 유의미한 예외 케이스마저 노이즈로 간주하여 과적합을 유발할 수 있음을 인지함. 결과적으로 무조건적으로 최신 및 최고의 모델을 따라가기 보다는 데이터의 본질적 한계와 형태를 정확히 분석하고, 이에 가장 알맞은 가벼운 모델부터 탄탄하게 설계해 나가는 데이터 중심 사고의 중요성을 깊이 배양하는 계기가 됨.


div align="center">
  <img src="https://github.com/user-attachments/assets/7b122045-1f40-4278-8d22-f10453e5f8af" alt="수상 이미지1" width="400" height="400" style="margin-right: 10px;">
  <img src="https://github.com/user-attachments/assets/cf308398-28ff-43d0-85f0-ef192fa0a317" alt="수상 이미지2" width="400" height="400" style="margin-left: 10px;">
</div>




