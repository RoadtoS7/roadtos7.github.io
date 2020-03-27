
---
title: "머신러닝을 위한 Pythonic Code 02 - List Comprehension"
date: 2020-03-27 23:51:24 -400
categories: MachineLearning
---

# 2. List Comprehension 이란?
- 기존의 리스트를 활용해서 간단하게 다른 리스트를 만드는 방법
- 한국어로는 '포괄적인 리스트', '포함되는 리스트' 라고 한다.
- 파이썬에서 가장 많이 사용되는 기법 중 하나이다.
- for문과 append()를 같이 사용하는 것보다 속도가 빠르다.
- 하지만 속도가 빠른 다른 방법들도 많아서 속도보다는 다른 목적으로 List Comprehension 을 사용한다.  

1) List Comprehension 을 사용하지 않는 경우

for문과 append()함수를 사용한다.
```
>>> result = []
>>> for i in range(10):
...     result.append(i)
...
>>> result
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

2) List Comprehension 을 사용한 경우
```
>>> result = [i for i in range(10)]
>>> result
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 1.2. 필터 기법이란?
- List Comprehension 뒤에 조건문을 넣어서 조건을 만족하는 값만 리스트에 넣는 기법
```
>>> result = [i for i in range(10) if i % 2 ==0]
>>> result
[0, 2, 4, 6, 8]
```

### 1.3. ListComprehension 을 이용한 중첩 for 문(Nested For Loop)
- ListComprehension 에서도 중첩 for문을 작성할 수 있지만, 결과물이 1차원 배열이다.
```
result = [i+j for i in word_1 for j in range(word_2)
>>> word_1 = "Hello"
>>> word_2 = "World"
>>> result = [i+j for i in word_1 for j in word_2]
>>> result
['HW', 'Ho', 'Hr', 'Hl', 'Hd', 'eW', 'eo', 'er', 'el', 'ed', 'lW', 'lo', 'lr', 'll', 'ld', 'lW', 'lo', 'lr', 'll', 'ld', 'oW', 'oo', 'or', 'ol', 'od']
```

### 1.4. List Comprehension 을 이용하여 중첩 For문을 만들 때에도 필터기법을 사용할 수 있다.
case_1 = []
