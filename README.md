# DonggeulDunggeul.github.io

https://donggeuldunggeul.github.io/


---
### github.io - Post
**1. /_posts 폴더에 md 파일 생성**
```
// 파일명
{{YYYY}}-{{MM}}-{{DD}}_{{Donggeul or Dunggeul}}_{{post이름}}
ex) 2022-07-16-Donggeul_post-name
```

**2. post 내용 작성**
```
---
title:  "[template] post template"          // 제목을 작성하지 않으면 .md 파일의 이름으로 작성
excerpt: "post template - first post"       // 

categories:
  - Post Template
tags:
  - [blog]

toc: true                                   // 목차 사용
toc_sticky: true                            // 생성된 목차가 스크롤을 따라 이동
 
date: 2022-07-16                            // 글을 처음 작성한 날짜
last_modified_at: 2022-07-16                // 글을 수정한 날짜
---
{{내용}}
```


### github.io - Local test
**1. git clone**
```
$ git clone https://github.com/DonggeulDunggeul/DonggeulDunggeul.github.io.git
```
**2. Local 실행**
```
$ bundle
```
```
$ jekyll serve
```

**3. 접속**
```
localhost:4000
```

---
### github.io - git Commit/Push
**1. git add**
```
// 전체 변경사항을 스테이지에 올림 
$ git add -A
```
**2. git commit**
```
$ git commit -m "{{커밋 메시지}}"
```

**3. git push**
```
$ git push
```

(참고) Window 환경에서 git add 시 CRLF 이슈 발생 시 명령어를 사용하여 설정 변경
```
$ git config --global core.autocrlf true
```
