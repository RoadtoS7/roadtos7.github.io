---
layout: post
title: "[Database] Key"
date: 2021-11-19 00:13:17 -400
lastmod: 2021-11-19 00:13:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories: 
  - DataStructure
background: "/img/classic_blue.jpeg"
---

## 정의
- 검색, 정렬시 튜플을 구분할 수 있는, 기준이 되는 속성(Attribute) 

## Key 종류
### :one: 슈퍼키 (Super Key)
- 테이블을 구성하는 모든 튜플에 대해  
`유일성` 만족 🔵  
`최소성` 만족 ❌  

- 튜플을 유일하게 구분하기만 하면 됨

#### :bulb: Key의 특성을 설명할 때 사용하는 용어 2
1. 유일성: 키가 튜플을 유일하게 식별한다.
2. 최소성: 튜플을 유일하게 식별하는데 꼭 필요한 속성들로만 구성 


### :two: 후보키 (Candidate Key)
- Tuple을 유일하게 식별하기 위해 사용하는 속성들의 부분 집합. 
- `유일성` 만족 🔵   
`최소성` 만족 🔵

- 슈퍼키 중에서 `최소성`을 만족하는 것

### :three: 기본키 (Primary Key)
- 후보키 중에서 선택한 메인 키

- 특징
  1. NULL 값을 가질 수 없다.
  2. 중복된 값을 가질 수 없다.

### :four: 대체키 (Alternate Key)
- 후보키 중에서 기본키를 제외한 나머지 키

### :five: 외래키 (Foreign Key)
- 다른 릴레이션의 기본키를 그대로 참조하는 속성의 집합 

<br/>