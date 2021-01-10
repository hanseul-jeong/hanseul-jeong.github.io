---
title : "[Study] 정보이론 Information Theory - 1.엔트로피 Entropy"
date : 2021-01-10 18:53:00
math : true
categories: [Study]
tags: [Study, 정보이론, Information Theory, Entropy]
---

논문을 읽다보면 굉장히 다양한 정보이론 개념들이 이용되는데 (특히 XAI논문!) 이를 하나씩 정리해보려고합니다.
'정보이론'은 사건(event) 간의 정보량에는 차이가 있다는 개념에서 출발합니다.
주사위가 1이 나오는 경우를 A 사건이라 하고, 로또에 당첨되는 경우를 B사건이라 했을 때 당연히 후자의 경우가 희박한 사건일 것입니다.
이렇게 사건간의 정보량의 차이를 정량적으로 계측 및 비교하기 위하여 많이 이용됩니다.

가장 기본적인 개념인 엔트로피 Entropy 부터 시작해보도록 하겠습니다.

## *Entropy*

엔트로피라는 용어는 물리학에서 먼저 접하신 분들이 많을 것이라고 생각합니다.
간단한 개념만 정리하면, '무질서도'라고 표현하며 에너지가 **유용하지 않은** 방향으로 변화하려는 현상을 설명할 때 **엔트로피가 증가한다** 고 표현합니다.

정보이론에서도 유사한 개념으로 사용하는데, '불확실성'으로 볼 수 있습니다.

 (1) 주사위를 굴려 숫자를 확인하는 경우
 (2) 로또에 당첨될/당첨되지 않을 경우

이 (1), (2) 사건 중 좀 더 불확실한(랜덤한) 경우는 (1)입니다.
(2)은 당첨될 확률이 극히 작기 때문에, (1)보다 확실하므로 (2)가 (1)보다 불확실한 정도가 낮습니다.
이렇게 사건 간의 혹은 한 사건 내에서의 경우 간 정보량을 비교할 때 많이 이용됩니다.

## *Quantity of Information (정보량)*

확률변수 \\(X\\) 에 속하는 \\(x\\)의 정보량은 다음과 같이 정의됩니다.

$$I(x)=-log(p_{x})$$

**놀람의 정도**로 해석하는데, 로또에 당첨될 확률 \\(p(X_{l}=x_{win})\\)는 주사위가 1이 나올 확률 \\(p(X_{d}=x_{1})\\)보다 낮습니다.
\\(log\\)함수는 non-negative 함수이기 때문에,

 $$\begin{aligned}
 p(X_{l}=x_{win}) &< p(X_{d}=x_{1})  \\
 -log(x_{win}) &> -log(x_{1})
 \end{aligned}$$

위의 정의를 통해 다음과 같이 각 사건의 정보량의 크기를 비교할 수 있습니다.

ML에서 entropy 개념을 접할 수 있는 것은 cross-entropy 혹은 KL-divergence가 대표적입니다.

## *Cross-entropy*

classification에서 흔히 사용되는 loss function으로,

$$J=\Sum_{c}-p_{c}^{\text{label}}log(p_{c}^{\text{pred}})$$

이를 잘 들여다보면 \\(J\\)를 줄이기 위해서는 각 클래스에 대한 label과 prediction의 정보량이 일치할 때 가장 작은 값이 나올 것이라고 생각할 수 있습니다.
 이는 결국 label의 확률분포와 pred의 확률분포가 일치하도록 만드는 것이 cross-entropy의 목적이라는 것을 알 수 있습니다.

# *KL-Divergence*

KL-divergence는 두 분포간의 차를 표현하기 위해 사용되는 개념입니다.

$$D_{KL}(p||q)=E\[log \frac{p}{q}\]$$

위의 식이 확률분포 \\(P,Q\\)간의 KL divergence의 정의이고 expectation을 풀어쓰면,

$$\begin{aligned}
D_{KL}(p||q) &= p (log \frac{p}{q}) \\
            &= -p(log \frac{q}{p}) \\
            &= -p(log(q) - log(p)) \\
            &= -p(log(q)) + p(log(p))
\end{aligned}$$

즉, 분포 \\(p, q\\) 간의 entropy 차와 분포\\(p\\)의 entropy의 합이라는 것을 알 수 있습니다.


