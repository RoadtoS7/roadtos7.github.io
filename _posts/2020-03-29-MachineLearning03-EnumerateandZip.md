---
date: 2020-03-29 23:03:07 -400
title: 머신러닝을 위한 Pythonic Code 03 - Enumerate & Zip
categories: MachineLearning
---

이번 포스팅에서는 Enumerate 함수와 zip 함수에 대해서 알아보겠습니다.  

<br/>
<br/>

## 1. Enumerate 
enumerate는 리스트의 각 요소를 인덱스와 함께 추출할 때 사용합니다.

예시 코드를 보면 더 정확히 이해가 될 것입니다.  
```
>>> for i, v in enumerate(['tic', 'tac', 'toe']):
...     print(i, v)
...
0 tic
1 tac
2 toe
```

enumerate는 문장에서 단어들의 위치를 추출할 때 유용하게 사용할 수 있습니다..  
1. 문장을 리스트로 만듭니다.  
2. 리스트를 enumerate를 이용하여 index와 리스트 요소로 unpacking 합니다.  
3. dict 타입으로 변환합니다.

위와 같은 과정을 거쳐서 문장에서 각 단어와 단어의 위치를 추출할 수 있습니다.  
코드로는 다음과 같습니다.
```
>>> {i:j for i, j in enumerate('Gachon University is an academic institue located in South Korea.'.split())}
{0: 'Gachon', 1: 'University', 2: 'is', 3: 'an', 4: 'academic', 5: 'institue', 6: 'located', 7: 'in', 8: 'South', 9: 'Korea.'}
```

텍스트 마이닝을 할 때 enumerate와 split함수 그리고 dict타입을 이용하여 리스트에서 번호를 붙여 추출할 수 있습니다.  

<br/>
<br/>
<br/>
<br/>

## 2. zip
zip은 여러개 리스트를 병렬적으로 추출할 때 사용합니다.  
여러개의 리스트에서 같은 인덱스 위치에 있는 값을 추출합니다.  
```
>>> for a, b in zip(alist, blist):
...     print(a, b)
...
a1 b1
a2 b2
a3 b3
```

### 2.1. List Comprehension 과 zip 을 동시에 사용할 수 있습니다.
```
# 리스트가 unpacking 되어 리스트의 각 요소인 tuple이 변수에 저장된다.
>>> a, b, c = [(1, 2, 3), (10, 20, 30), (100, 200, 300)]
>>> print(a, b, c)
(1, 2, 3) (10, 20, 30) (100, 200, 300)


```







