---
layout: post
title:  "Android - 데이터 바인딩 라이브러리(Data Binding Library): 환경 설정 및 예시"
date:   2020-08-10 14:12:17 -400
lastmod : 2020-08-10 14:12:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
---

이 포스팅은 Android Architecture Components 중 하나인 데이터 바인딩 라이브러리(Data Binding Library)에 대한 시리즈 포스팅 중 두번째 포스팅에 해당합니다.  
데이터 바인딩 라이브러리에 대해서 처음 접하시는 분들은 첫 번째 포스팅인 [데이터 바인딩 라이브러리(Data Binding Libarary) 소개](https://roadtos7.github.io/android/2020/08/07/Android-DataBindingLibrary.html)를 참고 부탁드립니다. :smile:


데이터 바인딩 라이브러리는 UI와 데이터를 연결하는데 기존의 프로그래밍적인 방식이 아니라, 선언적인 방식으로 이를 가능하게 함으로써, 성능이 뛰어나고, 유지보수가 쉬우며, 테스트가 용이한 앱을 만드는데 도움을 주는 라이브러리 입니다.  

이번 포스팅에서는 데이터 바인딩 라이브러리를 사용하기 위한 환경설정 방법에 대해 다뤄보고, 간단한 예시를 통해서 데이터 바인딩 라이브러리의 사용법을 알아보도록 하겠습니다.  

## 데이터 바인딩 라이브러리 환경설정
데이터 바인딩 라이브러리를 사용하기 위해서는 앱 모듈의 `build.gradle`파일에 다음과 같이 `dataBinding` 요소를 추가합니다.
```
android {
        ...
        dataBinding {
            enabled = true
        }
    }
    
```
