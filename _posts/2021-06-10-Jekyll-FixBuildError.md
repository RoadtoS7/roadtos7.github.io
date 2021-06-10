---
layout: post
title: "Github 블로그 page build error 해결 후기"
date: 2021-06-10 16:12:17 -400
lastmod: 2021-06-10 23:12:17 -400
sitemap:
  changefreq : daily
  priority : 1.0
categories: Android
background: "/img/classic_blue.jpeg"
---

## Github page build error
<img width="700" alt="스크린샷 2021-06-10 오후 4 15 11" src="https://user-images.githubusercontent.com/57262833/121481383-09a01500-ca07-11eb-9097-8e4433e24ea3.png">  

<br/>

어느날부터 master 브랜치에 post를 올려도 블로그에 띄워지질 않았다.  
그리고 계속해서... 이메일만 반복해서 받았다🤦‍♀️🤦‍♀️🤦‍♀️  

구글링을 해보니, 보통 jeykll 블로그 page build fail 되면, 이유와 함께 메일에 이유가 함께 온다는데.. 내 메일에는 아무런 이유도 표시되어 있지 않았다.  
더 많은 정보를 보고 싶으면 링크를 클릭하라니.. 일단 클릭해서 들어가 보았다...   

<br/>

<img width="500" alt="스크린샷 2021-06-10 오후 4 20 23" src="https://user-images.githubusercontent.com/57262833/121482096-c2feea80-ca07-11eb-9880-a7ae7040e4cc.png">

<br/>

해석을 해보자면 다음 4가지로 인해서 발생하는 경우가 많으니 이것을 우선 확인해보라는 것이었다.  
### 1️⃣ jekyll에서 지원하지 않는 플러그인을 사용하고 있을 수 있다. jekyll에서 지원하지 않는 plugin은 사용해선 안된다.  
[플러그인 목록에 들어가서](https://jekyllrb.com/docs/plugins/your-first-plugin/) `_config.yml`파일과 `Gemfile`에 지원안되는 plugin을 써놓은 것은 아닌지 확인해보았다. 대조 결과 이문제는 아니었다.  

### 2️⃣ 블로그 포스팅을 작성하는 repository가 허용 용량을 넘었을 수 있다.
repository의 허용 용량은 1GB미만이고, 어쩔 수 없다면 5GB까지 허용된다.
내 블로그는 500MB도 되지 않았다.  

### 3️⃣ `source`세팅파일에 해당하는 `_config.yml`파일을 변경했다면, Github Page가 이 파일을 오버라이딩 하기 때문에 `_config.yml`파일에서 에러가 발생했을 수도 있다는 내용이었다.
`_config.yml`파일에서 다음 목록들을 어겼지는 않은지 확인해보자 
<img width="500" alt="스크린샷 2021-06-10 오후 4 33 30" src="https://user-images.githubusercontent.com/57262833/121483842-98159600-ca09-11eb-8bc2-faec56279db0.png">

1. 공백을 넣을 때는 스페이스바 대신에 탭을 써야 한다.
2. `:`콜론 뒤에는 반드시 스페이스를 넣어야 한다.  ex) `timezone: Africa/Nairobi`
3. UTF-8 문자만 사용해야 한다.
4. `title`과 같은 문자열을 할당해야 하는 프로퍼티들에는 더블 쿼티션`"`을 사용해야 한다.
5. 여러 줄로 작성해야하는 것이 있다면, `|`이 new line표기이니 `|`를 사용하자.

이 5가지도 확인후 모두 적용했다.
 
### 4️⃣ 포스팅 파일 네임에 `:` 콜론이 포함되면 안된다. 
포스팅 파일에 `:`이 존재하는 것은 없었다.

그렇지만... page build가 되지 않는 것은 마찬가지였다....

[Troubleshooting Jekyll build errors for GitHub Pages sites](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/troubleshooting-jekyll-build-errors-for-github-pages-sites#troubleshooting-build-errors) 에 있는 걸 차례차례 해보기 시작했다.
차례차례 하던 중 highlighter language 부분에서 에러의 원인을 찾을 수 있었다.

Github history를 차례차례 보면서 어느 시점부터 page build가 되지 않았는지를 확인해보니 hightlangage 플러그인을 `Rouge`로 바꾼 이후, 이 플러그인 문법대로 작성한 포스팅부터 페이지 빌드가 안되고 있었다.
   
💡 참고   
> Github에서 블로그포스팅을 하는 레포지터리의 commit history를 보면 각각의 pull request 단위 혹은 commit 단위로 github page build가 성공하면 초록색 체크 표시가 뜨고, 그렇지 않으면 빨간색 체크 표시가 뜨는 것을 확인할 수 있다.  
  

<img width="1191" alt="스크린샷 2021-06-10 오후 4 55 27" src="https://user-images.githubusercontent.com/57262833/121487081-b335d500-ca0c-11eb-8eac-061b784d1b98.png">

따라서 우선 두 포스팅을 지우고 다시 pull request를 날려보니,,,

<img width="949" alt="스크린샷 2021-06-10 오후 5 05 36" src="https://user-images.githubusercontent.com/57262833/121488526-1b38eb00-ca0e-11eb-8750-bfee53bc9abb.png">
이렇게 page build가 다시 되는 것을 확인할 수 잇었다!!!
  
`Rouge` 문법을 준수하지 않고, 작성할 경우 page build 에러가 발생할 수 있다..!
만약 [Troubleshooting Jekyll build errors for GitHub Pages sites](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/troubleshooting-jekyll-build-errors-for-github-pages-sites#troubleshooting-build-errors) 에 있는 걸 차례차례 해보았는데도 잘 되지 않는다면, 
github history에서 빌드가 되지 않은 시점부터 commit한 파일들을 찬찬히 찾아보며 이유를 찾는 방법도 있다.  

