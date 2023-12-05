layout : post
author : jayiuk
tags : ["blog"]

# 구글 애널리틱스

---

## 구글 애널리틱스란?
![alt text](https://4011092965-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-M-w1z_NmDJj_bLdi8oT%2F-M6hAg88_piV3pcKs8T4%2F-M6lqt8OK2CV7M-vK1pD%2F%E1%84%80%E1%85%AE%E1%84%80%E1%85%B3%E1%86%AF-%E1%84%8B%E1%85%A2%E1%84%82%E1%85%A5%E1%86%AF%E1%84%85%E1%85%B5%E1%84%90%E1%85%B5%E1%86%A8%E1%84%89%E1%85%B3-%E1%84%85%E1%85%A9%E1%84%80%E1%85%A9.jpg?alt=media&token=56ff2110-c0ce-4680-91cf-5fd319387db7)
 - 웹사이트 트래픽을 추적하고 보고하는 웹 애널리틱스 서비스

## 깃허브 블로그에 적용 방법
### 1. Google Analytics 계정 설정
 1. [구글애널리틱스](https://analytics.google.com)으로 가 계정 생성
 2. 새 웹사이트에 대한 속성 생성
 3. 추적 ID를 받음
### 2. Jekyll 블로그에 코드 추가
 1. `_config`파일을 찾습니다.
 2. analytics 관련 설정을 찾아 적절히 수정(보통 추적 ID를 넣으면 됩니다)
 3. 관련 설정이 없는 경우 직접 추가해야 합니다. 보통 `_includes` 폴더 내에 `head.html` 파일을 사용합니다.
 4. 추적 코드 스니펫을 `</head>`태그 바로 앞에 붙여줍니다

```
 {% if site.google_analytics %}
 <script async src="https://www.googletagmanager.com/gtag/js?id={{ site.google_analytics }}"></script>
 <script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', '{{ site.google_analytics }}');
 </script>
 {% endif %}
```
 5. 모든 변경사항 커밋 후 푸시

---

## 실제 사용하기
 - 데이터 수집은 자동. 대시보드에서 보면 됩니다.