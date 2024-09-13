---
layout: post
title: git message CRLF (^M) issue
description: git format을 설정하는 방법과 ^M issue 해결 방법
post-image: ../assets/images/gitCRLFimage.png
author: 강준우
tags:
- git
- format
- message
- CRLF
---

git Format 설정하는 방법과 command 창에서 git commit 실행 시 줄 띄움 자리에 ^M 이 나타나는 issue 해결 방법

## git format 설정 방법

1. vim 설치
2. .gitmessage.txt 생성
3. format 붙여넣기 - __CRLF, LF 이슈 발생 지점__
4. git config 설정

### vim 설치

나는 window 를 사용해서 chocolatey를 사용해서 간단히 다운받았다.

```shell
choco install vim
```

### .gitmessage.txt 생성

.gitmessage.txt 파일을 생성한다.

```shell
vim .gitmessage.txt
```

### format 붙여넣기

아래 내용을 포맷으로 사용했다.

```shell
####################
# <타입> : <제목> 의 형식으로 제목을 아래 공백줄에 작성
# 제목은 50자 이내 / 변경사항이 "무엇"인지 명확히 작성 / 끝에 마침표 금지
# 예) feat: 로그인 기능 추가
# wdg: widget. 설명할 위젯을 적는다.
# ctt: content. 위젯이 아닌 내용을 적는다.
wdg: 
ctt: 
# 바로 아래 공백은 지우지 마세요 (제목과 본문의 분리를 위함)

####################
# 본문(구체적인 내용)을 아랫줄에 작성
# 여러 줄의 메세지를 작성할 땐 "-"로 구분 (한 줄은 72자 이내)
- 
####################
# 꼬릿말(footer)을 아랫줄에 작성
# 예) Close #7
# Video Num ex) # 7.6 - Chapter 7의 6번째 영상
# Video TimeLine ex) 7:34 7분 34초 부터 설명. 
Video Number: 	# A.B
Video TimeLine:	# mm:ss
Issue:		# 
####################
# feat : 새로운 기능 추가
# fix : 버그 수정
# docs : 문서 수정
# test : 테스트 코드 추가
# refact : 코드 리팩토링
# style : 코드 의미에 영향을 주지 않는 변경사항
# chore : 빌드 부분 혹은 패키지 매니저 수정사항
####################
```

### git config 설정

git message의 template을 설정하겠다고 git 에게 알려주어야한다.

```shell
git config --global commit.template .gitmessage.txt
```

## 오류

아래 그림처럼 줄 띄움 자리에 ^M이 나타나는 것을 볼 수 있다.

![^M image](../assets/images/M%20Issue.png)

원인과 해결방법은 아래와 같다.

### 원인: 
    (추측) git이 linux기반으로 작성되어 txt 파일을 읽을 때 LF로 작성된 형태로 파일을 읽는 것 같다.  

### 해결 방법:
    - VSCode IDE 아래 CRLF로 된 부분을 클릭하여 LF로 변경
    - 파일을 다시 저장
    - git config 다시 설정

다했다면 아래와 같이 잘 완료된 것을 볼 수 있다.
![img.png](../assets/images/M%20Issue%20resolved.png)