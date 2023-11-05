# 블로그 만드는 법

## 목차
1. github에 레포지토리 만들기
2. Jekyll 설치 및 초기 설정
3. vscode로 프로젝트 열기
4. 깃 연결 및 푸시
5. 꾸미기

### github에 레퍼지토리 만들기
레포지토리 이름을 [사용자명].github.io로 합니다.
저같은 경우엔 사용자명이 jayiuk라 jayiuk.github.io로 했습니다.

### Jekyll 설치
Ruby를 설치해줍니다. [Ruby installer](https://rubyinstaller.org/)
Start Command Prompt with Ruby 실행
```bash
gem install jekyll bundler
```
실행
새로운 Jekyll 사이트 생성
```bash
jekyll new [사용자명].github.io
```
cd를 사용해 생성된 디렉토리로 이동

### vscode로 프로젝트 열기
File > Open (또는 Open Folder on Windows)를 선택하고, 위에서 생성한 Jekyll 사이트 디렉토리를 선택합니다

### git 연결
```bash
git init
```
```bash
git remote add origin https://github.com/[사용자명].github.io.git
```
이렇게 연결시켜줌
그리고 커밋 후 푸시하면 됩니다
```bash
git add .
git commit -m "commit message"
git push origin main
```

### 블로그 꾸미기
[Jekyll Themes](http://jekyllthemes.org/)에 가서 테마를 고름
해당 테마의 github나 웹페이지로 가서 사용방법 확인
테마마다 방법이 서로 다름
제가 선택한 WhatATheme 테마의 경우
1. [Repository](https://github.com/thedevslot/WhatATheme/)를 포크합니다
2.  
```bash
git clone https://github.com/thedevslot/WhatATheme.git
```
을 통해 다운로드 합니다
3. 
```bash
bundle install
```
디렉토리 안에서 위의 명령어를 실행합니다
4. 
```bash
bundle exec jekyll s
```
를 통해 WhatATheme을 호스트합니다.

이 방법을 통해 블로그에 테마를 적용시켰습니다.
이 테마를 사용하실 분은 참고하시면 됩니다.