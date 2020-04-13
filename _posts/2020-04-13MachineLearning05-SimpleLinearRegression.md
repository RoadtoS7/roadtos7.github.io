---
title: "텐서플로우로 시작하는 딥러닝 기초02 - Linear Regression"
date: "2020-04-13 16:43:17 -400"
categories: MachinLearning
---

## 목차
1. Regression
2. Linear Regression
3. Hypothesis(가설, 가설함수)
4. Which hypothesis is better?
5. Cost, Cost function (비용, 비용함수)
6. Goal: Minimize cos (비용최소화)

<br/>
<br/>
<br/>
<br/>

### 2.1 Regression(회기)란?
Regression 이란 우리나라 말로 '회기', '복귀'를 의미합니다. 하지만 한국어의 뜻으로만 이해하면 원래의 뜻과 매우 다른 의미가 됩니다.  
사실 이 단어는 "Regression toward the mean"을 줄인 단어입니다.  
즉, 큰 데이터가 나오든 작은 데이터가 나오든 결국은 전체 데이터의 평균값으로 회귀하려는 특징을 가지고 있다는 의미입니다.  
이러한 특성을 우리는 Regression 으로 부릅니다.  

### 2.2 Linear Regression(선형 회귀)
일련의 데이터가 있을 때, 이 데이터들을 가장 잘 대표하는 직선의 방정식을 찾는 것 입니다.  
직선의 방정식을 구성하고 대표하는 것은 기울기와 y절편이라고 볼 수 있습니다.  
따라서 Linear Regression 은 일련의 데이터를 잘 표현하는 직선의 방정식의 기울기와 y절편을 구하는 것이라고 이야기할 수 있습니다.  

선형회귀의 대표적인 예시는 시험에 투자한 시간과 시험점수입니다.  
일반적으로 시험에 투자한 시간이 많을 수록 시험점수가 높습니다.  
이렇듯 어떤 값들이 선형적인 증가 혹은 감소 관계에 있을 때 이 관계를 해석하는 것이 Regression 입니다.

H(x) = Wx + b
우리는 데이터 집합을 만날 때 이 데이터 집합을 가장 잘 표현하는 직선의 방정식을 세우게 됩니다.  
이 때 이 직선의 방벙식이 가정(Hythothesis)입니다.
앞으로 우리는 기울기와 y절편을 각각 W와 b라고 부르겠습니다.
