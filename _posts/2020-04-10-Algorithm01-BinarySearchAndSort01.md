---
title: "Algorithm 01 - BinarySearch & Sort 01"
data: "2020-04-10 19:19:07 -400"
background: "/img/classic_blue.jpeg"
categories: Algorithm
---
# Algorithm 01 - BinarySearch & Sort 01
## 1.1 이진 검색(Binary Search)
- 배열에 데이터들이 오름차 순으로 저장되어있을 때 사용합니다.
- **즉, 데이터들이 정렬되어 있을 때 사용합니다.

### 저장 필요한 데이터
1. 시작 인덱스
2. 끝 인덱스
3. 가운데 인덱스
4. target 데이터
5. 오름차 순으로 정렬된 list 데이터 

```
def binarySearch(n, data, target):
  begin = 0
  end = n - 1
  while(begin <= end):
    middle = (begin + end) // 2
    
    if data[middle] == target:
      return middle
    elif data[middle] < target:
      start = middle + 1
    else:
      end = middle - 1
    
    
  return None
```

## 1.2 버블 정렬
```
def bubbleSort(data, n):
  for i in range(n-1, 1):
    for j in range(0, i):
      if data[j] > data[j+1]:
        data[j], data[j+1] = data[j+1], data[j]
```
