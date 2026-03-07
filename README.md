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
  <img src="https://github.com/user-attachments/assets/ac3f8627-5ad8-4f8a-bf33-15061a5c26d1" alt="이미지 1" width="500" height="400" style="margin-left: 10px;">
  <img src="https://github.com/user-attachments/assets/6b00126a-be47-4c4d-9b8a-e1f720f6bd43" alt="이미지 2" width="500" height="400" style="margin-left: 10px;">
</div>

**본선전** 

* 상관관계 + 인과관계 하이브리드 검증으로 우연히 비슷하게 움직이는 노이즈 제거를 위해 그랜저 인간관계 검정 도입
  
* 상관계수 * 데이터수 제곱근 * 인과성 가산점(bonus)을 결합한 자체 Score 체계를 구축하여 신뢰도 높은 페어만 추출

<div align="center">
  <img src="https://github.com/user-attachments/assets/e1d7e724-9d9f-4df3-a6eb-6c5e1c273378" alt="이미지 1" height="250" style="margin-right: 10px;">
  <img src="https://github.com/user-attachments/assets/f10628d5-0203-4cfc-8d8e-1facc031e9cc" alt="이미지 2" height="250" style="margin-left: 10px;">
</div>










