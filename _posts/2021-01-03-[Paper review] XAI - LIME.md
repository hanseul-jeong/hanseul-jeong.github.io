---
title : "[Paper review] XAI - 1.LIME"
date : 2021-01-03 11:53:00
math : true
category : [Papers]
tags : [XAI, Review]
---
## Introduction

설명가능한 인공지능분야에서 가장 널리 사용되는 method 중 하나인 [LIME](https://arxiv.org/pdf/1602.04938.pdf,"Why Should I Trust You?": Explaining the Predictions of Any Classifier, KDD16")을 리뷰해보도록 하겠습니다.

간단하게 설명가능한 인공지능 (Explainable AI, XAI)에 대해서 설명하자면,
Deep learning은 데이터와 결과 간의 상관관계를 모델이 추론하기 때문에
결과를 도출하는 과정은 사람이 이해하기 힘들다는 점에서 흔히 "블랙박스"라고 표현합니다.

성능이 좋은 것도 물론 중요하지만, 1) 모델이 판단한 근거가 사람이 납득 가능한지
또한 2) 트레이닝 데이터뿐만 아니라 전체 모집단에 대해 유효한 가정인지 등 실제 실생활에 적용하기 위해서
모델을 검증하는 것이 필요합니다.

이를 위해서, 모델이 추론하는 근거 및 과정을 사람이 이해할 수 있도록 Deep learning 모델을 설명하는 방법론을 XAI라고 합니다.

XAI라고 하면 어려워 보이지만, 사실 간단하게는 입력 영상 중 어느 부분을 중점적으로 봤는지 간단하게 표현하는 방식도 XAI에 속합니다.
아래 그림은 [CAM (Class Activation Map)](http://cnnlocalization.csail.mit.edu/Zhou_Learning_Deep_Features_CVPR_2016_paper.pdf, "Learning Deep Features for Discriminative Localization, CVPR16") 논문 속 그림으로
![img CAM](/assets/img/post/cam.PNG)

양치질, 벌목 클래스에 대해서 어느 부분을 활성화시켜서 결과를 도출했는지를 확인할 수 있습니다.

Vision분야에서는 CAM, [Grad-CAM](https://openaccess.thecvf.com/content_iccv_2017/html/Selvaraju_Grad-CAM_Visual_Explanations_ICCV_2017_paper.html, "Grad-CAM: Visual Explanations From Deep Networks via Gradient-Based Localization, ICCV17"), [Grad-CAM++](https://ieeexplore.ieee.org/document/8354201, "Grad-CAM++: Generalized Gradient-Based Visual Explanations for Deep Convolutional Networks, WACV18") 등이 모델결과를 설명하기 위해서 논문에서 많이 사용됩니다.
Vision뿐만 아니라 다양한 데이터 및 모델에 적용하기 위해 **Model agnostic**한 방법론들이 고안되었는데,
그 중에 대표적인 **LIME (Local Interpretable Model-agnostic Explanations)** 논문을 설명드리려고 합니다.



## Abstract

Title : “Why Should I Trust You?” Explaining the Predictions of Any Classifier

일단 제목을 직관적으로 잘 지었다고 생각이 드는데, 모델의 신뢰성을 검증하기 위해서 XAI가 필요하고, 이를 위해 어떤 종류의 모델에도 적용가능한 방법론을 제시한 논문입니다.
![img LIME1](/assets/img/post/lime_1.png)

> XAI를 통해 사람의 의사결정을 도와줄 수 있다. LIME 논문에서 발췌한 그림입니다 (Figure 1).

논문에서는 설명에서 필요한 특성 (Desired Characters)을 다음과 같이 정리하였습니다.

**1) interpretable;** 입력과 그 결과를 "사용자가 이해할 수 있는" 설명을 제공해야 한다.

**2) local fidelity;** 유사한 데이터에 대해서는 유사한 설명이 이뤄져야 한다.

**3) model-agnostic;** 어떤 형태의 모델에도 적용할 수 있어야 한다.

**4) global perspective;** 모델에 대한 전반적인 설명이 필요하다.

(1),(2)를 설명에서 필수적인 특성으로 꼽았고, 이를 기반으로 방법론을 구성하였습니다.
LIME은 두 가지 방식으로 설명하는데, 첫 번째는 입력 attribute의 중요도(importance)를 찾는 방식. 두 번째는 중요한 attribute set을 선택하는 방식입니다.
각각 LIME, SP-LIME(Submodular Pick)으로 표현합니다.
![img LIME2](/assets/img/post/lime_2.png)
![img LIME3](/assets/img/post/lime_3.png)

> 각각 [lime github](https://github.com/marcotcr/lime)과 논문 그림이다 (Figure 4).



## Method of LIME
원리는 다음의 그림과 간단한 수식으로 정리됩니다.

![img LIME4](/assets/img/post/lime_4.png)
> 논문 Figure 3 참고

그림에서 하늘색과 분홍색 배경은 deep learning인 모델 \\(f\\)의 복잡한 결정경계를 표현합니다.
굵은 빨간색의 + 샘플에 대해서 설명하려고 할 때, 국소적인 지역(Locally)에서만 설명할 수 있는 interpretable한 모델 \\(g\\)를 근사시켜서 설명하는 방식입니다.
샘플을 중심으로 주변 데이터를 랜덤하게 생성하고, 그 샘플에 대해서 원 모델 \\(f\\)와 같은 결과를 내도록 학습시키면 샘플 주변에서는 모델의 추론 방식과 같게 학습될 것입니다.

$$\xi(x)=\text{argmin}_{g \in G} L(f,g,\pi_{x}) + \Omega(g)$$

추가적으로 생성하는 interpretable한 모델의 복잡도 (Complexity)를 \\(\Omega\\)로 정의하고 loss에 포함시켜서, 사용자가 이해할 수 있는 설명을 만들도록 제한을 두었습니다.
> tree의 경우 tree의 깊이, linear model의 경우 non-zero weight의 수 등...

또한 원래 모델과 유사하게 학습시키기 위해서 생성한 랜덤 데이터들의 가중치 \\(\pi_{x}\\)를 거리에 따라 다르게 두어 최대한 원 모델과 비슷하게 학습시킬 수 있게 하였습니다.

$$L(f,g,\pi_{x}) = \sum_{z,z' \in \mathbb{Z}} \pi_{x}(z)(f(z)-g(z'))^{2}$$

설명하고자 하는 샘플과 유사한 데이터들을 생성하기 위해서 원본 데이터에 각 attribute를 포함할지 제외할지를 1 또는 0으로 표현한 binary mask인 \\(z'={\\{0,1\\}}^{d'}\\)를 생성합니다.
이렇게 생성한 \\(z'\\)를 기반으로 실제 데이터 차원에서 샘플을 생성한 것을 \\(z \in \mathcal{R}^{d}\\)로 표현합니다.
즉, 원 모델과 비슷한 결과를 내도록 모델 \\(g\\)를 근사시킵니다.

이 때 가중치는 \\(\pi_{x}(z)=\text{exp}(-D(x,z)^{2}/\sigma^{2})\\) 이렇게 정의되고, \\(D\\)는 task에 따라 정의되는 거리함수를 의미합니다.



## Method of SP-LIME
Submodular pick은 설명에 요구되는 특성 중 4) global perspective를 표현하기 위한 방식입니다.
입력 값 중에서 어떤 set이 결정에 중요한지를 표현해서, 특히 vision 데이터에 대해서 한 눈에 알아볼 수 있도록 설명할 수 있다는 장점이 있습니다.

총\\(n\\)개의 샘플 \\(X=x_{1},\cdots,x_{i},\cdots,x_{n}\\)에 대해서 학습시킨 각각의 \\(g_{i}\\)함수들의 weight를 바탕으로 최종적인 global importance를 생성합니다.
weight를 matrix로 표현한 것을 **explanation matrix** \\(W_{ij}\\)로 표현하는데, \\(i\\)는 각 샘플 \\(j\\)는 각 attribute를 의미합니다.
explanation matrix를 기반으로, 각 attribute의 중요도 \\(I_{j}=\sqrt{\sum_{i=1}^{n} W_{ij}}\\) 입니다. (논문에서는 **feature importance**로 표현합니다.)

![img LIME5](/assets/img/post/lime_5.png)
> 논문의 Figure 5 참고

행(row)마다 다른 샘플으로 각각 2개씩의 attribute를 중요하다고 판단하였습니다. 이 때 열(column)기준으로 보면 \\(f2\\)인 점선으로 표현된 부분이 가장 빈번하게 중요하다고 판단됨을 알 수 있습니다.
feature importance가 높은 순으로 총 \\(B\\) 개의 attribute를 선택하여 set을 구성합니다.


## Results

XAI 방법은 그 성능을 증명하기가 어렵다는 점에서 한계가 있습니다. 가장 중요한 특성이 "user의 수준에 맞는 설명"이기 때문에 설명을 잘했는지 못했는지를 객관적으로 입증하기가 어렵습니다.
대부분 정성적인 방식으로 Amazon Mechanical Turk (AMT)를 통해 우수성을 입증합니다.
이를 제외하고, 이 논문에서 흥미로웠던 실험은 객관적인 결과를 보여주기 위해 중요한 attribute들로 학습시킨 성능을 report했다는 점입니다.
LIME의 결과로 중요도가 높은 attribute를 최대 10개 이용하여 모델을 학습시킨 후 그 성능을 비교하였습니다.

![img LIME6](/assets/img/post/lime_6.png)
> 논문의 Figure 6,7 참고

Linear Regression, Tree 모델, 그리고 두 데이터셋에서 모두 LIME의 recall이 가장 높은 것을 확인할 수 있습니다.

또 하나의 흥미로운 실험으로 논문을 마무리하는데요,
Deep learning 모델의 분류 결과가 잘못될 경우에 설명모델은 어떤 결과를 내는지에 대한 실험입니다.

![img LIME7](/assets/img/post/lime_7.png)
> 논문의 Figure 11 참고

강아지 허스키에 대해서 늑대로 분류한 경우의 원본 사진이 (a)인데, 이 때 모델은 흰 눈 배경을 중요하다고 보았습니다.
이러한 설명을 통해 모델의 취약점을 파악하고, deep learning모델의 수정 및 개선도 기대할 수 있다는 insight를 주었습니다.



감사합니다.
