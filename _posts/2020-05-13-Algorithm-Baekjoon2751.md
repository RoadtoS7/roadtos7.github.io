---
title: "Baekjoon 2751번 - 수 정렬하기 2"
date: "2020-05-13 23:07:17 -400"
background: "/img/classic_blue.jpeg"
categories: Algorithm
---

- O(nlogn) 알고리즘으로 풀어야한다.
- 합병 정렬(merge sort), 힙 정렬(heap sort) 로 풀어야 한다.

```
v = [int(input()) for i in range(int(input()))] # 입력
 
# merge sort
def merge(left, right) :
    v=list()
    i=0;j=0
    while(i<len(left) and j<len(right)) :
        if left[i]<=right[j] :
            v.append(left[i])
            i+=1
        else :
            v.append(right[j])
            j+=1
    if i==len(left) : v = v + right[j:len(right)]
    if j == len(right): v = v + left[i:len(left)]
    return v
 
def merge_sort(v) :
    if len(v) <= 1 : return v
    m = len(v)//2
    left = merge_sort(v[0:m])
    right = merge_sort(v[m:len(v)])
    return merge(left, right)
 
m = merge_sort(v)
print(*m, sep="\n")
```
