---
layout: post
title: jekyll server 세팅하기
description: jekyll liveserver를 위한 세팅
post-image: ../assets/images/rke2_image.svg
author: 강준우
tags:
- Jekyll
- liveserver
- gem
- bundle

---
## Requirements

### Ruby(gem) 설치

각자 OS에 맞는 Ruby를 설치하는데 필자는 Windows라서 [Ruby Installer](https://rubyinstaller.org/)에서 다운받아 사용하였다. 

```bash
ruby -v
gem -v
gcc -v 
g++ -v
make -v
```




### Jekyll과 Bundler 설치

``` bash
gem update --system
bundle add webrick
gem install jekyll bundler
```


### 폴더 이동 후 실행

``` bash
cd % gemfile이 있는 위치(폴더) %
bundle install
bundler exec jekyll serve --livereload
```

--livereload 옵션은 저장 혹은 자동으로 서버를 refresh 시켜서 블로그 변화를 실시간으로 볼 수 있게 해준다.