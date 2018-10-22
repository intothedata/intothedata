---
title: "3 sigma rule"
---

![그림](/images/3_sigma_rule/Standard_deviation_diagram.svg.png)

데이터의 분포를 정규분포로 가정하고(또는 우기고) 평균으로 부터 ±표준편차 * 시그마계수를 벗어나면 아웃라이어(outlier, 이상치)라고 판단하는 것을 말한다. 너무 단순한 것이라고 최근에는 그대로 사용하는 경우는 거의 없지만 단순하면서로 의외로 그럴듯하게 잘 작동한다.

시그마계수는 표준편차에 얼마를 곱할 것인가를 말하는데 보통 2 ~ 3의 값을 사용한다. 참고로 6시그마는 6을 곱하는 것인데 99.9999998027%의 범위밖을 말한다. 이와 관련된 것은 위키피디아의 [68-95-99.7_규칙](https://ko.wikipedia.org/wiki/68-95-99.7_규칙)을 참고하기 바란다.

보통 흔히 쓰이는 것이 2시그마(2sd 또는 2 sigma)인데 해석하면 평균에서 2시그마 만큼까지의 좌위 범위내에 전체의 95%가 포함되게 된다.

위 그림에서는 -2시그마와 2시그마 사이를 말한다.

이것을 응용해서 위의 범위를 벗어난 양쪽 꼬리 부분에 들어 있다면 아웃라이어(outlier), 즉 이상치라고 간주해서 간단한 이상치 감지 모형을 만들어서 사용할 수 있다.

2시그마(2*표준편차)를 사용할 것인지 3시그마(3*표준편차)를 사용할 것인지는 하는 사람이 경험적으로 선택하지만 보통 2.5시그마 정도에서 시작해서 경험적 또는 실험적으로 값을 바꿔나가면서 이상치(outlier)를 찾는다.

## 참조

[위키피디아](https://ko.wikipedia.org/wiki/68-95-99.7_%EA%B7%9C%EC%B9%99)