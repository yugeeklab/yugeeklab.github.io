---
title: "(Paper) PointMixup"
date: 2021-09-23 23:11:00 +0900
categories: [Paper, Cloud Point]
tags: [cloud point, data augmentation]
math: true
---

이미지에서 적용되는 방법인 Mixup을 Point Cloud에 적용하려는 시도가 PointMixup이다. Mixup이 왜 Point Cloud에 적용되지 않는지를 밝히고 Point Cloud에 Mixup을 도입하기 위한 방법을 제시한다.



# 왜 기존의 Mixup은 Point Cloud에 적용되지 않는가?

왜냐하면 Point Cloud에는 정규 표현 방법이 없기 때문이다. 무슨 소리냐면 이미지의 경우 픽셀 그리드라는 표현 방법이 존재한다. 따라서 A라는 이미지와 B라는 이미지를 같은 위치의 픽셀끼리 더할 수 있다. 하지만 Raw Point Cloud는 그렇지 못하다. Point Cloud를 삼차원 좌표계에 표현한다고 하자. A 포인트 클라우드와 B 포인트 클라우드를 더한다고 하자. 어떻게 정의할 것인가? 떠오르는 가장 쉬운 방법은 집합의 덧셈을 하는 것이다. 하지만 이렇게 되면 교집합이 하나도 없을 경우 새로 생성된 포인트 클라우드 C는 점이 두배로 늘어난다. 만약 [0,1]의 수(λ)를 하나 뽑아서 λ만큼의 점을 A에서 반영하고 (1-λ)만큼의 점을 B에서 반영하면 이 문제를 해결할 수 있다. 하지만 Mixup은 label도 Mix해야한다. 위의 방법처럼 mix를 하면 label도 합리적으로 믹스할 수 있는지 의문이 남는다.

위에서 이야기한 방법은 그냥 내가 갑자기 생각나서 써본 것인데 논문을 자세히 읽어보니 논문에도 소개된 방법이었다. 논문에서는 이 방법을 point sampling interpolation이라고 한다.

앞서 말했듯이 Label까지 합리적으로 믹스할 수 있는가?라는 문제가 있다고 했다. 이 논문에서는 Label을 합리적으로 선형결합할 수 있는 Point Cloud의 믹스 방법을 제시한다.

# Shortest-path Interpolation

![image-20210926181053486](../../assets/img/2021-09-23-point-mixup/image-20210926181053486.png)


위의 그림과 같이 shortest interpolation을 하면 라벨을 보다 합리적으로 믹스할 수 있다. 오른쪽 그림의 경우 믹스한 것들끼리 중복될 여지가 있다. 라벨은 전혀 다른데 말이다.

## Earth Mover's Distance

shortest interpolation을 어떻게 구현하나요? 이 논문에서는 Earth Mover's Distance를 사용한다.

두 확률 분포간 거리를 구하는 것에는 많은 방법이 있다. 수업 시간에 배운 KL 다이벌전스도 그 중 하나다. Earth Mover's Distance는 A라는 확률 분포를 B라는 확률 분포랑 같게 만들기 위해 필요한 최소양이다. 
$$
{\phi^*} = argmin_{\phi} \sum_i||x_i - y_{\phi(i)}||_2
$$

$$
d_{EMD} = {1 \over N} \sum_{i} ||x_i - y_{\phi^*(i)}||_2
$$


# Optimal assignment interpolation

Optimal assignment란 Earth Mover's Distance를 구하는 과정에서 생성된 매칭이다.
$$
즉 \phi^*을 뜻한다.
$$
그리고 다음과 같이 Mix한다.
$$
S_{OA}^{(\lambda)} = \{(1-\lambda)\cdot x_i + \lambda \cdot y_{\phi^*(i)}\}_{i=1}^N
$$

## 증명



# Manifold PointMixup: Interpolate between latent point features

feature 단에서 Mixup을 할 수도 있다.
$$
Z_{(l), OA}^{(\lambda)} = \{(x_i^{mix}, z_i^{mix})\},\\ x_i^{mix} = (1-{\lambda}) \cdot x_i + \lambda \cdot y_{\phi^*(i)}, \\ z_i^{mix} = (1-\lambda) \cdot z_i^{(x)} + \lambda \cdot z_{\phi^*(i)}^{(y)}
$$
