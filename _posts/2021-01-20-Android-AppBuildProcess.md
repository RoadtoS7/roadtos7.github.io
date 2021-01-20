---
layout: post
title:  "Android - 안드로이드 앱 빌드 과정(Android App Build Process)"
date:   2021-01-20 21:58:17 -400
lastmod : 2021-01-20 21:58:17 -400
sitemap :
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

## 안드로이드 앱 빌드 과정
안드로이드 앱을 빌드하는 과정에는 많은 프로세스와 툴이 사용된다.  
안드로이드 앱의 빌드 과정은 매우 유연하기 때문에, 이 프로세스가 어떤식으로 작동하는지를 이해해두면 유용합니다.  

- 기본적인 안드로이드 앱 빌드 과정 사진   
출처: 구글 

![기본적인 안드로이드 앱 빌드 과정](https://developer.android.com/images/tools/studio/build-process_2x.png?hl=ko) 

위 그림은 본적인 안드로이드 앱의 빌드 과정을 나타내며 이는 다음과 같은 순서를 따른다.  
1. 컴파일러가 소스코드를 `DEX(Dalvik Executable) 파일`로 변환한다. `DEX 파일`은 안드로이드 기기에서 실행되는 바이트 코드를 담고 있다.  
소스코드 외의 모든 것은 컴파일된 리소스로 변환한다.
  
2. APK 패키저가 DEX 파일들과 컴파일된 리소스를 합쳐서 하나의 APK 파일로 만든다.
- 이 APK 파일로는 안드로이드 기기에서 설치될 수 없다. APK 파일을 배포하고 설치할 수 있으려면 APK파일에 서명을 해야한다.

3. APK 패키저가 Debug용 키 스토어 혹은 Release 용 키스토어를 가지고 APK 파일에 서명한다.  
    - Debug 버전 앱을 빌드하는 경우라면(테스트 혹은 프로파일링 전용 앱) Debug용 키 스토어를 가지고 서명한다.
    - Release 버전 앱을 빌드하는 경우라면(외부에 출시할 앱) Release용 키 스토어를 가지고 서명한다.
    
4. 최종 APK를 만들기 전에 APK 패키저는 zipalign(집얼라인) 툴을 이용해서 앱이 기기에서 실행될 때, 메모리를 많이 사용하지 않도록 최적화 한다.

5. 빌드 프로세스가 끝나면 우리는 외부에 배포, 테스트, 릴리즈할 수 잇는 릴리즈용 apk나 혹은 디버그용 apk를 얻게 된다.

<br>
<br/>

> 이 포스팅은 안드로이스 공식 문서를 바탕으로 만들어졌습니다.  
