+++
title = "정규분포, 가우시안 분포 - Normal Distribution, Gaussian Distribution"
alwaysopen = true
+++

위키피디아의 [정규분포](https://ko.wikipedia.org/wiki/%EC%A0%95%EA%B7%9C%EB%B6%84%ED%8F%AC)를 보면 설명이 잘 되어있다.

보통 정규분포를 따른다고 하는 표현을 다음과 같이 표기한다.

$$X \sim \mathcal{N}(\mu,\,\sigma^{2})\,.$$

정규분포 함수 공식은 다음과 같다.

$$f(x) = \frac 1 {\sigma\sqrt{2\pi}} e^{-\frac{(x-\mu)^2} {2\sigma^2}}$$

또는

\\[P(x) = \frac{1}{{\sigma \sqrt {2\pi } }}{e^{{{ - {{\left( {x - \mu } \right)}^2}} \mathord{\left/
 {\vphantom {{ - {{\left( {x - \mu } \right)}^2}} {2{\sigma ^2}}}} \right.
 } {2{\sigma ^2}}}}}\\]

위의 공식은 정규분포에서의 \\(x\\)의 밀도를 알려주는 함수를 만드는 공식이다. 즉 x값을 입력할 때 그 x값에 해당하는 확률 밀도를 리턴하는 함수이다.

쉽게 설명하면 만약 어떤 데이터가 있고 그 데이터에 대한 평균과 표준편차를 알고 있다고 하자. 그렇게 하면 정규분포를 생성할 수 있다. 정규분포의 모양은 사실 평균과 표준편차에 따라 달라지는 것이기 때문이다. 그렇게 하고나면 그렇게 만들어진 정규분포에 x값의 위치에 따른 y값을 알아낼 수 있다. 이때 이 y값이 x가 나올 확률이 된다.

그래서 위의 식 자체로 함수가 완성된 것이 아니다. 위의 공식에서 \\(\sigma\\)와 \\(\mu\\)를 넣어주어야 하는데 평균과 표준편차를 구해서 넣어주면 된다.

[R vignett](https://cran.r-project.org/web/packages/ExtDist/vignettes/Distributions-Normal.html)

