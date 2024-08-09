---
title: LiveReload EventMachine loadError 해결법
layout: post
post-image: "https://github.com/June222/June222.github.io/blob/blog/assets/images/LiveReloadEventMachineError.png?raw=true"
description: 3번의 원인 모를 이유로 정상 작동 중 멈춰버린 livereload. 해결법을 기록하고자 씁니다.
tags:
- window
- jekyll
- livereload
- eventmachine
- ruby

---

--livereload 옵션 없이는 되지만 옵션을 걸면 안되는 LiveReload의 EventMachine Error 해결법 시작합니다.

* [목차 1](haha)
* [목차 2](gaga)

---

# Error Message

![EventMachine Error bash](https://github.com/June222/June222.github.io/blob/blog/assets/images/LiveReloadEventMachineError.png?raw=true) <br/>
아래와 같이 Error Message가 뜬다면 잘 찾아왔다. 
```shell
Unable to load the EventMachine C extension To use the pure-ruby reactor, require 'em/pure_ruby'
require: cannot load such file -- 3.0/rubyeventmachine (LoadError)
```
<br/>

# 정석 해결책
EventMachine에 문제가 있다고 하니 재설치를 해보자. 

## EventMachine 재설치 - [Blog Link](https://robbinespu.gitlab.io/posts/jekyll-unable-load-eventmachine/) <br/>
    
요약하자면 다음과 같다. 

1. `gem uninstall eventmachine` 입력 후 'eventmachine-1.2.7-x64-mingw32' 삭제
2. `Gemfile`에 맨 아래에 아래 코드 추가
```shell
gem 'eventmachine', '1.2.7', git: 'git@github.com:eventmachine/eventmachine', tag: 'v1.2.7'
```
3. `bundle install` 입력
4. `bundle exec jekyll clean` 입력
5. `--livereload` 옵션 넣고 사용 가능함.

성공적으로 됐다면 좋아요를 눌러주고 가세요! <br/><br/><br/>

## EventMachine 재설치 후에도 안됨.

그런데 난 위에서 해결이 안됐다. 정말 많은 해결 방법을 찾아봤지만 해결이 되지 않았고 모든 것을 재설치하기로 마음을 먹었다. 
아래 링크를 따라 재설치하면 된다. <br/>
[jekyll 공식 홈페이지 링크](https://jekyllrb-ko.github.io/docs/installation/windows/) <br/>

### 도중에 발견한 점들

- **C, D에 ruby 폴더 전부 삭제.** 난 C, D 드라이브가 나누어져있는데 용량이 부족해 D 드라이브에 프로그램을 깔 때가 있다. 다운과 삭제를 중복하며 삭제되지 않은 폴더가 두 드라이브를 합쳐 총 4개의 폴더가 있었다.
- **WSL 사용 안함** wsl은 사용하지 않고 모든 다운로드를 진행했다.
- **Ruby 버전은 하위 버전**으로,  **Ruby Version은 3.0.7**로 다운 받았다. [Ruby 다운 Link](https://www.ruby-lang.org/en/downloads/)
- [jekyll Link](https://jekyllrb-ko.github.io/docs/installation/windows/)를 따라 jekyll, bundler 다운

전 여기서 되었습니다. 도움이 되었기를 바래봅니다. 감사합니다.

