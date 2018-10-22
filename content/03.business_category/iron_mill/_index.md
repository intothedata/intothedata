+++
title = "철강 - Iron mill"
+++

철강회사 또는 제철사를 말한다. 국내회사는 POSCO(포스코, 구 포항제철), 현대제철(구 INI Steel)이 있다.  제철과 제강은 다르다고 하는데 제강은 고철을 사용해서 강을 만들고 제철은 철광석을 녹여서 철을 만드는 것이다.

탄소함량이 2% 이하인 것은 강(steel)이라 하고 그 이상인 것은 철(iron)이라고 한다고 알려져 있다. 즉 제철회사는 철을 생산할 수 있어야 하는 것이고 강도 생산할 수 있지만 제강회사는 철을 생산하지 못하는 경우를 말한다.

철강회사는 팩토리(factory)라고 하지 않고 밀(mill) 즉, 우리말로 직역하면 “방앗간”이 되는 것인데 이렇게 부르는 것은 원재료(철광석)를 받아서 다른 것의 재료가 되는 것 2차 재료를 가공하는 것을 하기 때문에 그런 의미에서 밀(mill)이라고 부는다고 한다. 즉 밀(여기에서는 곡물을 말함)을 받아서 방앗간에서 밀가루를 만들고 그 밀가루로 빵을 만드는 것처럼 밀(mill)사들은 재료를 받아서 최종 생산물의 재료가 되는 중간 재료를 만드는 회사를 말한다. 보통 밀(mill)사는 많은 플랜트(Plant) 위에 많은 팩토리를 가지고 있으며 기간산업인 경우가 많아서 사업체와 비즈니스의 규모도 매우 크다.

철강회사는 냄비, 솥단지, 식칼 같은 것을 직접 생산하지는 않기 때문에 일반 소비자가 직접 거래를 할 일이 없다.  때문에 실제로 철강과 관련된 일을 실제로 하지 않는 일반인이나 경험이 없는 사람에게는 데이터사이언스와 관련지어서 거리감이 있고 현실감이 떨어질 수 있다.

하지만 실제로 그 큰 규모때문에 여러가지 얽힌 것들이 많아서 다뤄야 할 것이 매우 많고 실제로 데이터사이언스가 매우 많이 필요한 산업이기도 하다.

## 관련 용어 및 배경 지식

### 고로

흔히 알고 있는 용광로를 말한다. 철광석을 녹여서 쇠로 만든다. 용광로에 대한 분석을 별도로 할 일은 별로 없다. 매우 중요하기 때문에 매우 잘 관리되고 있는 편이다. 데이터사이언스가 개입할 여지가 사실 별로 없다.

### 전기로

폐철이나 고철을 녹여서 쇠로 만든다.  전기를 사용해서 솥단지를 가열하기 때문에 온도가 낮아서 철광석을 녹이지 못한다.  전기를 물쓰듯이 쓰기 때문에 효율이 좋지 않다. 아직까지는 데이터사이언스가 개입할 여지가 별로 없다.

### 열연

높은 온도에서 쇠를 가공하는 것을 열연이라고 한다.

### 냉연

낮은 온도에서 쇠를 가공하는 것을 열연이라고 한다. 냉이라는 글자가 있어서 차가운 상태에서 가공하는 것이 아니라 열연보다는 상대적으로 낮은 온도에서 가공한다는 의미이다.

### 특수강

특수합금을 주로 취급하는데 초강고도를 요구하는 특별한 부품에 필요한 합금소재를 만들거나 부품 그 자체를 말한다.

## 철강회사에서 필요한 데이터사이언스

### 생산라인 데이터 분석

### 원자재 가격 예측

보통 니켈의 가격을 예측하는 것으로 알려져 있다.

쇠를 만드는데 철광석만 필요한 것은 아니다. 니켈도 필요한데 니켈이 철강의 가격을 좌우하는 큰 요인중의 하나이다. 마치 금 가격을 예측해서 쌀 때 사두고 비쌀때 파는 것처럼 철강회사도 니켈의 가격을 예측해서 보유량을 관리한다.

즉, 단순하게 니켈이 쌀 때 구매해서 비쌀때 사용하는 것이다.  쌀 때 많이 사두고 비쌀 때 덜 사야 하는데 니켈의 가격에 여러 요인이 있어 가격의 변동을 예측하는 것이 매우 어렵다고 알려져 있다.

보통 시계열 모형(time-series model)로 접근을 하는 사례가 많이 알려져 있지만 발표된 것은 현실성이 없는 것들이 많다.