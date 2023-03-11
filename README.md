# [Dacon] 쇼핑몰 지점별 매출액 예측 경진대회

# 1. Introduction

## 소개 & 성적

### Score  
![public 1st](https://img.shields.io/badge/PUBLIC-1st-red?style=plastic) ![private 11th](https://img.shields.io/badge/PRIVATE-11th-red?style=plastic)


### Members

임승섭|신은빈
:-:|:-:
|[seopp](https://github.com/seopp)|[eunbinni](https://github.com/eunbinni)|
|y7511204@naver.com|dnflrhra@naver.com|

---
# 2. Project Outline


## 대회 개요


> 쇼핑몰 매장별 프로모션 정보, 주변 날씨, 실업률, 연료 가격 등의 정보를 바탕으로 쇼핑몰의 각 지점별 주간 매출액을 예측


## 대회 기간 
> **2022.07.01 ~ 2022.08.01**

## 평가 방법 : RMSE

## 대회 데이터셋

> train.csv  
- id : 샘플 아이디
- Store : 쇼핑몰 지점
- Date : 주 단위(Weekly) 날짜
- Temperature : 해당 쇼핑몰 주변 기온
- Fuel_Price : 해당 쇼핑몰 주변 연료 가격
- Promotion 1~5 : 해당 쇼핑몰의 비식별화된 프로모션 정보
- Unemployment : 해당 쇼핑몰 지역의 실업률
- IsHoliday : 해당 기간의 공휴일 포함 여부
- Weekly_Sales : 주간 매출액 (목표 예측값)


> test.csv
- id : 샘플 아이디
- Store : 쇼핑몰 지점
- Date : 주 단위(Weekly) 날짜
- Temperature : 해당 쇼핑몰 주변 기온
- Fuel_Price : 해당 쇼핑몰 주변 연료 가격
- Promotion 1~5 : 해당 쇼핑몰의 비식별화된 프로모션 정보
- Unemployment : 해당 쇼핑몰 지역의 실업률
- IsHoliday : 해당 기간의 공휴일 포함 여부

> sample_submission.csv
- id : 샘플 아이디
- Weekly_Sales : 주간 매출액 (목표 예측값)


# 3. Retrospect


## 회고
우선 머신러닝을 공부한지 얼마 안되어서 처음으로 진행중인 대회에 참가한 것이었기에 미숙한점이 매우 많다.  
물론 머신러닝 초심자를 위한 Dacon Basic 대회이기에 상금도 없고 연습용 대회 느낌이었지만 머신러닝에 대해 아무것도 모르는 초보자의 입장에서는 참가하면서 배울게 많은 대회였던 것 같다.

- 마주쳤던 문제들
    - 스케일링
        - 로그 변환, StandardSclaer, MinmaxScaler 등등 알고 있는 여러 가지 방법들을 모두 시도해보았다. 
        - 하지만 결국 마지막 모델을 선택하는 과정에서 XGBoost, LightGBM, CatBoost와 같은 Tree기반의 모델들을 선택했기 때문에 결론적으로 큰 의미가 없는 과정이었다.
        - 실제로 스케일링을 진행한 모델과 그렇지 않은 모델을 비교한 결과 결국 Tree기반의 모델을 사용함에 있어서는 스케일링이 영향이 없었다.
        - 물론 몇몇 분들은 그럼에도 불구하고 스케일링을 진행했을 때 Tree기반 모델에서도 성능 향상이 있었다고 한다. 
        - 일리가 없는 말은 아니지만 나의 경험과 여러 가지 자료들을 종합해 보았을 때 Tree기반 모델에서는 스케일링이 영향이 없다고 판단했다.
        
    - 45개 지점마다 각각 모델 만들기
      - 데이터를 살펴보면 결국 각 지점마다의 주간 매출액을 예측하는 것인데 각 지점별로 매출액 편차가 큼을 알 수 있었다.
      - 다른 변수들의 영향보다 Store 즉, 지점이 무엇이냐가 굉장히 크게 관여를 하는 것으로 판단했다.
      - 따라서 지점별로 나누어 각각 모델을 따로 만들고 예측을 해서 예측값들을 합치는 방향으로 모델링을 진행했다.
      - 하지만 모델링을 진행하면서 "지점별로 데이터들을 나누면 각 지점마다의 데이터는 139개의 행으로 구성되어 있는데, 이 적은 데이터만 가지고 예측 성능을 뛰어나게 할 수 있을까?"와 같은 의문이 들었다.
      - 결론적으로 성능이 좋아지긴 했으니 이 방법을 택했다. 이러한 이유를 나름대로 생각해보자면 변수들의 영향보다 Store가 가지는 영향력이 매우 컸기 때문에 데이터가 적음에도 불구하고 예측 성능이 매우 떨어지지 않았다고 생각한다. 
      - 이 부분에 대해서는 추가적인 깊은 학습이 필요할 것으로 생각된다.

    - Feature Selction 
        - Feature Selection의 방법론은 크게 3가지로 구분된다.
            - Filter Method : Feature 간 관련성을 측정하는 방법
            - Wrapper Method : Feature Subset의 유용성을 측정하는 방법
            - Embedded Method : Feature Subset의 유용성을 측정하지만, 내장 metric을 사용하는 방법

        - 이러한 방법들이 있지만 대회 진행 당시 학습이 부족했던 관계로 위의 방법론들을 사용해보지 못하였다.
        - 따라서 상관관계를 통해 나름의 Feature Selection을 진행했다.

- Public Score와 Private Score 간의 차이에 관한 고찰
    - 데이콘 대회는 Test 데이터 셋에 대한 모델의 성능에 따라 순위가 결정된다.
    - Public Score는 제출할 때마다 확인 가능한 점수이다. (해당 점수는 항상 평가 페이지에 설명되어 있는 통계적 평가 지표를 사용하여 계산됨.)
    - 그러나 이 Public Score는 Test 데이터 셋의 일부(본 대회에서는 50%)에서만 결정된다. 이것은 공개 순위표이며 대회 중 어느 정도의 상대적인 성과를 보여준다.
    - 대회가 종료되면 선택할 제출물을 선택하고 Test 데이터 셋에서 Public Score에 반영되지 않았던 나머지 데이터들을 가지고 Private Score를 계산한다.
    - Private Score는 점수에 대한 지속적인 피드백을 받지 못하므로 비공개 리더보드인 것이다. 
    - 최종 대회 순위는 Private Score를 기반으로 하며, 우승자는 Private Score가 가장 높은 사람이다.
    - 최종 순위가 Private Score를 기반으로 하는 이유는 Public Score를 기반으로 할 경우 해당 데이터의 특정 노이즈에 과적합되는 모델이 될 위험이 있기 때문이다. 
    - 현실에서는 과적합을 피하는 것이 중요하다.
    - 본 대회를 참가하면서 나는 Public Score만을 올리기에 집중했다. 사실 대회를 진행하면서도 Public과 Private의 차이점을 제대로 알지 못했었기 때문이다.
    - Public Score를 높이기 위한 모델링을 진행하다보니 지나치게 과적합이 되었고, 결국 Private Score에서 높은 등수를 기록하지 못했다.
    - 결국 우리가 현실 세계에서 풀어야 할 문제는 우리가 알지 못하는 미지의 데이터이고, 본 대회와 같이 여러번 테스트해보면서 모델을 향상시키기 어렵다.
    - 따라서 과적합되지 않은 모델을 만드는 것을 최우선으로 생각하고 이를 위해 다양한 방법들을 고안하고 시도해야 할 것이다.
