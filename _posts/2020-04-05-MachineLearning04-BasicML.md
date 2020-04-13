---
title: "텐서플로우로 시작하는 딥러닝 기초01: Basic ML"
date: "2020-04-13 14:41:17 -400"
categories: MachinLearning
---

## 목차
1. 머신러닝(ML)이란 무엇인가?
2. 러닝(Learning)이란 무엇인가?  
    - supervised
    - unsupervised
3. 회귀(regression)이란 무엇인가?
4. classification 이란 무엇인가?

### 1.1 머신러닝(Machine Learning)
일반적인 프로그램으로는 해결할 수 없는 문제들이 너무 많습니다.  
예를 들어서, 스팸메일을 거르는 프로그램을 짜려고 할 때 스팸메일에 해당하는 규칙이 너무 많아서 스팸메일 필터링 프로그램은 개발하기 여렵습니다.  
또 다른 예로는 자율주행자동차가 있습니다.  
자율 주행자동차의 경우에도 고려해야하는 경우의 수가 너무나도 많기 때문에 일방적인 프로그램의 형태로는 개발하기 어렵습니다.  

따라서 이러한 문제를 해결하기 위해서 Arthur Samuel은 컴퓨터가 스스로 해결방법을 배우면 어떨까 라는 생각을 하게 됩니다. 이러한 Arthrur Samuel 의 생각으로부터 Machine Learning은 출발하게 됩니다.  

곧 Machine Learning은 데이터를 보고 학습해서 능력을 갖는 프로그래밍을 의미합니다.

### 1.2 Learning(학습)
프로그램이 데이터를 보고 학습을 해야하는데, 학습의 방법에 따라서 두 가지 종류의 학습이 존재합니다.  
1. Supervised Learning : labeling이 된 데이터 셋(집합)을 가지고 학습을 하는 것을 SuperVised Learning 이라고 합니다.  
2. Unpervised Learning: label을 우리가 만들어주는 것이 아니라, labeling되지 않은 데이터를 가지고 스스로 학습하는 것입니다.


이 포스팅에서는 SuperVised Learning에 대해서 먼저 다루겠습니다.
Supervised Learning 예는 다음과 같습니다.  
1. Image Labeling: 고양이, 강아지와 같이 labeling 된 이미지로부터의 학습을 의미합니다.
2. Email spam filter: 이미 이메일 가운데 어떤 이메일이 스펨인지 스펨이 아닌지 labeling이 된 이메일을 가지고 학습합니다.
3. Predicting exam score: 이전에 시험을 친 사람들이 준비한 시간 데이터와 점수를 가지고 학습하는 것을 말합니다.

<br/>
<br/>
<br/>

Supervised Learning에서는 따라서 학습을 위해 labeling된 데이터 셋이 필요합니다.  

<br/>
<br/>
<br/>
<br/>

### 1.4 Supervised Learning의 종류
Supervised Learning에도 종류가 있습니다.
1. regression
ex. 시험에 투자한 시간을 바탕으로 시험 점수 예측하는 경우
즉, 0~100 사이의 시험점수와 같은 것을 예측하는 것을 regression 이라고 합니다.  

2. binary classification
ex. 투자한 시간을 바탕으로 시험의 Pass/Non-Pass를 예측하는 경우
Pass/Non Pass 와 같이 두가지로 나누는 결과를 예측하는 것을 binary classification 이라고 합니다.  

3.  multi-label classification
ex. 투자한 시간을 바탕으로 시험의 등급을 예측하는 경우
시험의 등급은 A, B, C 등 결과가 여러가지로 나올 수 있지만
나올 수 있는 결과는 A, B, C, F와 같이 시험점수에 비해서 적은 수 입니다.  
이런 경우를 multi-label classification 이라고 합니다.  

그렇다면 이중에서도 regression의 예시인 투자한 시간을 가지고 시험점수를 예측하는 경우를 더 자세히 살펴보겠습니다.  

x(hours)|y(score)
:---:|:--:
10|90
9|80
3|50
2|30

시험점수를 예측하는 경우에는 이런식의 데이터 셋이 필요합니다.  
이런 데이터를 학습한 모델을 가지고 7시간을 공부했을 때 어떤 점수가 나올지 예측할 수 있습니다.
이런 모델을 regression 모델이라고 합니다.   

다음으로는 binary classification의 예시인 Pass/Non Pass 의 예시에 대해서 살펴보겠ㅅ브니다.

x(hours)|y(pass/fail)
:---:|:--:
10|P
9|P
3|F
2|F

이런 형식의 데이터 셋을 학습한 모델은 사용자가 공부한 시간을 입력했을 때, 그에 대한 pass/non pass 결과를 예측합니다.
이런 모델을 binary classfication 이라고 합니다.

그렇다면 multi-label classfication 모델의 데이터 셋에 대해서도 예상이 되실 것 같습니다.
예측하는 데이터 결과가 A, B, C, F 와 같이 정해져있지만 결과가 2개보다 더 다양하게 나올 수 있는 경우를 multi-label classfication 이라고 합니다.  
예시 데이터 셋은 다음과 같습니다.

x(hours)|y(grade)
:---:|:--:
10|A
9|B
3|C
2|C

다음 포스팅에소는 이 세가지 Learning 기법 중에서 Linear Regression 에 대해서 더 자세히 알아보도록 하겠습니다.  
<:x(hours):|y(pass/fail)
---
10|P
9|P
3|F
2|F

<br/>
<br/>
<br/>
<br/>

> 이 포스팅은 'BoostCourcse'의 '텐서플로우로 시작하는 딥러닝 기초' 강의를 바탕으로 작성되었습니다. 

