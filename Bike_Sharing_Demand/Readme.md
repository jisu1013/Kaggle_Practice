## Bike Sharing Demand

### 대회의 목표

시간대별 바이크 대여량을 예측하는 것이 목표이다. 

이때, training data는 달의 1일부터 19일까지 주어지고 test data는 20일부터 말일까지다. 주어진 data의 feature들은 다음과 같다.

- datetime
- season
- holiday
- workingday
- weather
- temp
- atemp
- humidity
- windspeed
- casual
- registered
- **count - number of total rentals :  이게 예측해야 하는 값이다 !!**

### 평가지표

Root Mean Squared Logarithmic Error (RMSLE)

$\sqrt{\frac{1}{n} \sum_{i=1}^n (\log(p_i + 1) - \log(a_i+1))^2 }$

### 진행 과정
처음에는 datetime feature 포함 없이 학습을 했고 그 결과 **RMSLE = 1.368**을 얻었다.

두 번째는 Hour, Year, Month, Day feature를 pandas datetime을 통해서 처리 후 학습에 사용했고,

그 결과 **RMSLE = 0.487**로 큰 폭으로 좋아진 결과를 얻었다 !!  

그 이유는 이 대회 자체가 시간대 별로 이용량을 예측하는 것이기 때문에 영향이 컸던 것 같다. 

학습에 tree model을 사용했기 때문에, feature importance를 통해서 확인했을 때 Hour의 importance는 0.61이고 나머지 feature의 importance는 0.08 이하였다.

Hour가 굉장히 중요한 역할을 하고 있었다. 다른 importance가 낮은 feature를 뺐을 때 오히려 낮은 성능을 얻었다. 

수치상으로는 필요없어보여도 좋은 학습을 하기 위해서 최대한 많은 feature를 고려해주는 것이 중요하다. 캐글에서는! 

오히려 importance가 굉장히 높은 feature들이 안 좋은 영향을 줄 수도 있기 때문에, 이후에 잘 고려해야한다. 

왜냐하면 Y 값에 큰 영향을 미치기 때문에, 새로운 data가 주어졌을 때 학습이 잘 안 될 수 있다. 

데이터를 볼 때 조금 의아했던 점은 Casual+Registered=Count인데, 왜 굳이 분리해서 제공했을까이다. 

이런 경우 Casual에 대해서 학습을 하고 예측을 한 뒤, 따로 Registered에 대해서 학습을 하고 예측한 결과를 더해서 예측할 수 있다. 

이 경우, 장점은 점수가 더 잘 나올 수 있다는 점이다. 특성을 더 잘 학습할 수 있기 때문이다. 하지만 단점은 모델이 여러개라서 무겁기 때문에 배포할 때 어렵다는 것이다. 최대한 가볍게 만드는 점도 중요한 것 같다. 물론 무거운 모델이 필요할 수도 있지만,,실제 서비스를 고려할 때 모델을 가볍게 만드는 것도 중요하다!

이 대회에서는 Day 특성이 오히려 성능을 안 좋게 만드는데, 그 이유는 모델은 Day feature를 중요하게 고려하여서 더 중요한 feature에 질문하지 못하고 낭비하였기 때문이다. 

이 대회에서는 1 ~ 19일에서 학습하고 20 ~ 30일까지 예측하기 때문에 day가 가지는 pattern을 활용하지 못한다.

그렇다면 Day 특성이 중요한 경우는?? Day 패턴이 선형적인 형태일 때이다. 지금과 같이 카테고리적인 느낌일 때는 도움이 되지 못한다 !!
