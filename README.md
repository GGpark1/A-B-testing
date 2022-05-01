# A/B tests with Statistics and Machine Learning

## 1. 프로젝트 목표 설정

### 1) 테스트 진행 전 상황

- 온라인 교육을 제공하는 Udacity를 대상으로 A/B 테스트를 진행
- 교육 정보를 알려주는 코스 개요 페이지에 1) Start Free Trial과 2) Access Course Material 선택지가 있음
    -  Start Free Trial을 선택하면 카드 정보 입력 페이지로 연결되고, 입력을 마치면 14일 뒤에 자동 청구됨
    - Access Course Material을 선택하면 무료로 영상을 보고 퀴즈를 풀 수 있지만 코칭이나 인증서 발급을 받을 수 없음 

### 2) A/B 테스트 진행 목적

- 학생이 'Start Free Trial' 버튼을 누르고 무료 평가판 교육을 진행할 시 교육에 할애할 수 있는 시간을 입력하도록 함
- 통상적으로 소요되는 시간보다 적은 시간을 입력 시 해당 교육에 더 많은 시간이 필요하다는 경고 메시지를 출력
- 이와 같은 기능을 추가했을 때 교육에 충분한 시간을 쏟을 수 없는 사람이 무료 평가판을 등록하지 않고 떠날 것으로 기대됨

### 3) 프로젝트 효과
- 무료 사용자의 비율을 낮추고 유료 사용자의 비율을 높임
- 코치는 수료가능성이 높은 학생에게 집중할 수 있음

### 4) 가설 설정
- 귀무가설 : 새로운 기능을 추가하더라도 무료 평가판을 등록하는 사람의 수는 변하지 않는다.
- 대립가설 : 기능을 추가하면 무료 평가판을 등록하는 사람의 수가 유의미하게 변화한다.

## 2. 프로젝트 평가 지표 설정

### 1) 불변 지표
- 두 집단을 편향없이 비교하기 위해 테스트 진행 과정에서 변하지 않고 유지되어야 하는 지표 설정
  - 코스 개요 페이지의 쿠키 수
  - 'Start Free Trial' 버튼 클릭 수

### 2) 평가 지표
- 새로운 기능의 효과를 확인하기 위해 필요한 지표
  - Gross Conversion : Start Free Trial 버튼 클릭 수 대비 무료 과정 등록 확률
  - Retention : 무료 평가 과정 등록 대비 유료 결제 확률
  - Net Conversion : Start Free Trial 버튼 클릭 수 대비 유료 결제 등록 확률

### 3) 평가 지표에 대해 baseline 설정
- 평가 지표 상의 유의미한 변화가 있더라도 그 변화가 비즈니스 차원에서 유의미한 수익률을 보장해야 함
- 기능을 변경하는 비용 대비 평가 지표의 향상 정도가 높지 않다면 기능을 변경할 이유가 없음
  - Gross Conversion : -0.01
  - Retention : 0.01
  - Net Conversion : 0.0075 

## 3. 샘플 사이즈 설정

### 1) 지표 당 필요한 샘플 사이즈 계산

적정한 신뢰 구간 안에서 두 집단의 차이를 비교하기 위한 샘플 사이즈 계산

1) Gross Conversion : 645,875 샘플
- Gross Conversion의 유의수준을 측정하기 위해서 25,835개의 Click 수(Cookies)가 필요함
- 현재 5,000 페이지 조회수(Cookies) 당 400개의 클릭이 발생하고 있음. 즉, 클릭 수 하나당 12.5개의 페이지 조회수가 필요함
- A/B 양 집단에 사용할 샘플이 필요하기 때문에 2를 곱함

2) Retention : 4,737,454 샘플
3) Net Conversion : 685,675

### 2) 최종 샘플 사이즈

- 하루에 약 40,000 건의 페이지 조회가 발생하고 있음. 각 지표에 필요한 샘플 사이즈를 얻기 위해 필요한 데이터 수집 기간은 다음과 같음
  - GC : 17일(645875 / 40000)
  - R : 119일(4737454 / 40000)
  - NC : 18일(685675 / 40000)
- 현실적으로 119일동안 A/B 테스트를 진행하기 어려움
- NC의 샘플 사이즈를 선택하면 GC의 샘플 사이즈 수도 포괄할 수 있음(Retention에 대한 테스트는 진행할 수 없음)
- **A/B 테스트를 위해 수집할 샘플의 사이즈는 685,675개로 선택**

## 4. 지표 평가

### 1) 불변 지표 평가

불변 지표는 두 집단에 유의미한 차이가 없다는 귀무가설이 채택되어야 함

- 전체 데이터에 대해 control과 experiment 데이터의 비율은 0.5이여야 샘플 사이즈에 편향이 없다고 볼 수 있음
- 0.5에 대한 허용 오차를 구하고, 전체 데이터에 대한 A/B 샘플 사이즈가 허용 오차 안에 위치하는 지 확인함
  - 코스 개요 페이지의 쿠키 수 : 통제군과 실험군의 비율이 95% 신뢰도에서 0.5 +-0.012 구간 안에 위치하는 것을 확인 -> 귀무가설 채택
  - 'Start Free Trial' 버튼 클릭 수 : 통제군과 실험군의 비율이 95% 신뢰도에서 0.5 +-0.041 구간 안에 위치하는 것을 확인 -> 귀무가설 채택

### 2) Effect size 방법을 활용한 지표 평가

A/B 집단의 평가 지표 간의 유의미한 변화가 있는 것을 확인해야 함

1) Gross Conversion

- 통제군과 실험군의 차이 : -0.0206
- 두 집단의 차이는 95% 확률로 [-0.0292 ~ -0.012] 안에서 형성될 가능성이 높음
- 신뢰 구간 안에 0이 포함되어 있지 않으므로 통게적으로 두 집단의 GC 차이는 통계적으로 유의미함
- 또한, 신뢰 구간의 범위가 baseline인 -0.01보다 작으므로 실질적으로도 유의미한 차이라고 볼 수 있음

2) Net Conversion

- 통제군과 실험군의 차이 : -0.0049
- 두 집단의 차이는 95% 확률로 [-0.0116 ~ 0.0018000000000000004] 안에서 형성될 가능성이 높음
- 신뢰 구간 안에 0이 포함되어 있으므로 두 집단의 차이는 통계적으로 유의하지 않을 가능성이 있음
- 또한, 신뢰 구간의 범위가 baseline인 0.075보다 작기 때문에 실질적으로도 유의미한 차이라고 볼 수 없음

### 3) 부호 검정(sign test)를 활용한 지표 평가

평균값을 기반으로 한 효과 크기 방식의 검정은 이상치에 취약하기 때문에 이상치의 영향을 덜 받는 sign test로 분석 결과를 cross check 함

1) Gross Conversion

- A/B 집단 간에 차이가 없다고 가정한다면 A와 B 집단의 GC 지표의 크기가 유사해야함
- 실험군의 GC 지표가 더 큰 경우의 수 : 4
- 특정 집단의 GC 지표가 4번만 클 수 있는 확률(유의확률) : 0.0026
- 유의확률이 0.05보다 작으므로 귀무가설을 기각함 -> A/B 집단 간에 유의미한 차이가 있음

2) Net Conversion 

- NC 지표에서 실험군이 더 큰 케이스 수 : 10
- 특정 집단의 NC 지표가 10번만 클 수 있는 확률(유의확률) : 0.6774
- 유의확률이 0.05보다 크기 때문에 귀무가설을 채택함 -> A/B 집단 간에 유의미한 차이가 있음

### 4) 머신러닝을 활용한 지표 평가

1) 모델 성능
- 데이터의 갯수가 적기 때문에 XGB의 성능이 현저히 떨어짐
- Tree 모델도 준수한 성능을 보였으나, Linear 모델의 성능이 더욱 뛰어난 것을 확인함
- 단순한 데이터에 대해서는 Linear 모델의 성능이 뛰어난 것을 알 수 있음

2) 특성 간 관계 분석
- 'Start free trial' 버튼 클릭 수가 많아질 수록 'Enrollments(과정 무료 등록)'는 감소함 
  - Enrollments 감소를 의도한 실험군 데이터가 섞여 있기 때문에 Enrollments가 감소한 것으로 보임
  - 즉, 새로운 기능이 Enrollments 감소에 유의미한 역할을 한다고 해석할 수 있음
- 'Course overview' 페이지 조회 수가 많아질 수록 'Enrollments(과정 무료 등록)'는 증가함
  - Course overview 페이지에는 Enrollments 감소를 위한 별도의 기능을 추가하지 않았기 때문에 페이지 조회수가 늘수록 Enrollments가 증가하는 것으로 볼 수 있음
  - Enrollments를 더 줄이려는 비즈니스 목적이 있다면 Course overview 페이지에도 새로운 기능을 추가하는 것이 좋음

- 실험군 데이터일 때 Enrollments의 감소가 나타남
  - 실험군 여부와 Enrollments의 회귀계수는 음수
  - 다만, 통계적으로 유의미한 수준은 아니기 때문에 해석에 주의가 필요함

3) Enrollments에 영향을 주는 주요 특성
- Pageviews와 Clicks이 Enrollments에 가장 큰 영향을 미침
- 비즈니스 목적을 달성하기 위해서 Pageview와 Click을 조절할 필요가 있음

## 5. 결론

- 새로운 기능을 사용하면 무료 사용자의 등록률을 낮출 수 있지만, 유료 사용자의 등록률을 높일 수는 없을 것으로 보임
- 새로운 기능이 유료 사용자의 비율을 높일 수 없으므로 **비즈니스 상의 이득이 크다고 볼 수 없음**
- 새로운 기능을 도입하지 않는 것을 제안함

## 6. 레퍼런스

- A/B tests with python : https://www.kaggle.com/code/tammyrotem/ab-tests-with-python/notebook
- 오차한계의 개념 및 구현 방법 : https://apcalculus.tistory.com/218
- 샘플 사이즈의 의미 : https://playinpap.github.io/sample-size-calculator-statistics/
- 지표에 대한 이해 : Udacity, A/B testing, Choosing and Characterizing Metrics(https://classroom.udacity.com/courses/ud257/lessons/456f424d-0cbc-481c-a0ab-a6217013f46e/concepts/bde635a8-6c93-4f14-8b5b-85387a072a8c)
