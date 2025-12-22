---
layout: post
title: gem SSL Verification Error 트러블 슈팅
description: 사내 웹필터로 발생한 SSL Verification
post-image: ../assets/images/sslVerificationError.png
author: 강준우
tags:
- gem
- SSL Verification
- ERROR
- Root Certificate
- x509
---

사내 웹필터 적용을 위해 Windows에 사설 CA를 추가하면서 jekyll 및 bundler가 gem으로 설치되지 않는 문제가 발생하였습니다. 

그 문제 원인을 찾고 해결하는 과정을 정리해보겠습니다.

### 0. 환경

**OS**: windows

**Shell**: PowerShell



### 1. 문제 상황

아래와 같이 `gem install` 명령어를 치는 경우에 발생한 error log입니다.

`x509` error 인 경우 해당 문제입니다.

```bash
gem install bundler jekyll 

ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  Could not find a valid gem 'bundler' (>= 0), here is why:
          Unable to download data from https://rubygems.org/ - SSL_connect returned=1 ***생략*** state=error: certificate verify failed (self-signed certificate in certificate chain) (https://rubygems.org/specs.4.8.gz)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  Could not find a valid gem 'jekyll' (>= 0), here is why:
          Unable to download data from https://rubygems.org/ - SSL_connect returned=1 ***생략*** state=error: certificate verify failed (self-signed certificate in certificate chain) (https://rubygems.org/specs.4.8.gz)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
ERROR:  SSL verification error at depth 1: self-signed certificate in certificate chain (19)
ERROR:  Root certificate is not trusted (/C=***생략***/ePrism SSL)
```

### 2. 문제 원인 찾기

사내 웹 필터를 적용하며 사설 CA를 사용하였고 이로 인한 문제가 발생한 것 같았습니다. 보이는 "/C=\*\*\*생략\*\*\*/ePrism SSL"는 현재 사설 CA가 위치한 경로입니다.

현재 Error Code도 말해주는 것은 **회사/게이트웨이("ePrism SSL")가 TLS 가로채기(SSL inspection)를 하고있다.** 라는 것입니다. 루비(gem)가 사용하는 CA 번들이 사내 루트 인증서를 모르니 "self-signed certificate in certificate chain / Root certificate is not trusted” 로 실패하는 것입니다.

해결 방법은 **"사내 루트 CA를 신뢰 목록에 추가"**하는 것입니다.

### 2-1. 왜 SSL 에러가 나는가?

원인과 해결 방법은 파악했으나 사실 무슨 말인지 전혀 모르겠어서 정리해보고자 합니다.

1. 인터넷에서 HTTPS(SSL/TLS) 통신을 할 때는, 인증서(=사이트 신분증)를 서버가 클라이언트(브라우저/프로그램)에게 보여줍니다.
2. 이 인증서를 신뢰할수 있는 기관(CA, Certificate Authority)이 발급했는지를 클라이언트가 확인합니다.
3. 보통 브라우저나 운영체제 안에는 글로벌 루트 CA 리스트(VeriSigh, DigiCert, Let's Encrypt 등)가 들어있습니다.
4. 그런데, 회사 보안 장비(웹필터, SSL Proxy, 방화벽 등)가 트래픽을 중간에서 가로채려면 원본 인증서를 그대로 주지 못합니다. 대신 자기가 만든 사설 CA로 서명한 새로운 인증서를 만들어서 클라이언트에게 줍니다.

👉문제는 여기서 발생하는 데 사설 CA는 전세계 공인 루트 목록에 없기 때문에, OS/브라우저/프로그램이 "이거 믿을 수 없어"라고 하고 SSL 오류를 냅니다.

### 2-2. 그래서 뭘 해야 하나?

- 클라이언트(OS나 프로그램)가 그 사설 CA도 신뢰하도록 설정해야합니다.
- 사설 CA 인증서를 OS의 신뢰 저장소에 추가 (이번 문제 원인 X)
- 프로그램의 CA번들 확인

### 3. 해결하기

이제 본격적으로 트러블 슈팅을 해봅시다. 아래 순서대로 진행하되 저는 단순히 Windows에 CA를 추가한다고 문제를 해결하지 못했습니다. 그러다가 3번 과정을 진행하면서 문제가 해결되었음을 알려드립니다. 2번 만으로도 끝나셨다면 축복 드립니다.

<br>

#### 1. 사설 CA 파일 확보
- 보안/네트워크 팀에서 CA 인증서 파일을 받습니다.
- 파일이 없다면, 웹 접속 시 브라우저에서 "인증서 보기 -> 내보내기"로 추출할 수 있습니다.

저는 제 사수에게 CA를 받았습니다.

<br>

#### 2. Windows에 CA 추가

1. `win + R` -> `mmc` 입력 -> 엔터
2. 메뉴 `파일` -> `스냅인 추가/제거`
3. 인증서(Certificates) 선택 -> 추가
4. "컴퓨터 계정" -> "로컬 컴퓨터" 선택 -> 완료
5. 왼쪽 트리에서 신뢰할 수 있는 루트 인증 기관 -> 인증서 선택
6. 마우스 우클릭 -> 모든 작업 -> 가져오기
7. 받은 인증서 선택 -> 마법사 완료
8. 닫기

👉 이제 Windows 전체가 ePrisme CA를 신뢰합니다.

하지만 저는 여전히 해결되지 않았습니다. 브라우저에서 인증서를 확인해보니 브라우저는 그 인증서를 신뢰하고 있다는 메세지를 확인하였습니다. 

그렇다면 OS나 브라우저에서의 문제가 아니라고 판단하고 원인 파악을 이어나갔습니다.

<br>

#### 3. Ruby가 올바른 CA번들을 쓰도록 설정

Ruby는 Windows에서 OpenSSL을 쓰는데, 기본 번들에 ePrism이 안 들어가 있으니 경로를 지정해줍니다.

1. CA 번들 경로 확인
    ```shell
    gem env

    # 루비가 어떤 번들을 보게 됐는지 확인
    ruby -ropenssl -e "puts 'USE=' + (ENV['SSL_CERT_FILE'] || OpenSSL::X509::DEFAULT_CERT_FILE)"

    # 아래와 같이 나오면 성공
    # DEFAULT_CERT_FILE=C:/Ruby34-x64/lib/ruby/3.4.0/etc/ssl\cert.pem 
    # DEFAULT_CERT_DIR=C:/Ruby34-x64/lib/ruby/3.4.0/etc/ssl\certs

    # Gem 소스 초기화(캐시 클리어)
    gem sources --clear-all
    ```
2. 사설 CA 합치기
   
    위에서 나온 `# DEFAULT_CERT_FILE` 경로의 pem 값을 notepad로 열고 기존에 받은 사설 CA도 notepad로 열어서 복사하여 맨 아래 붙여넣기를 합니다. 참고로 `-----BEGIN CERTIFICATE-----` 로 시작해야하고, `-----BEGIN CERTIFICATE-----` 부터 `-----END CERTIFICATE-----`까지 모두 복사해야합니다.

3. 확인 및 설치 재시도

    여기까지 정상적으로 완료했다면 아래 명령어를 복사하여 다시 재설치 해봅시다.
    ```shell
    # Ruby가 어떤 cert 번들을 쓰는지 확인
    ruby -ropenssl -e "puts 'USE=' + (ENV['SSL_CERT_FILE'] || OpenSSL::X509::DEFAULT_CERT_FILE)"

    # Gem 캐시 초기화
    gem sources --clear-all

    # 설치 (상세 로그 확인)
    gem install bundler jekyll -V
    ```

### 4. 결론

<br>

#### 1. gem 서버 접근 준비
- 기본 서버: `https://rubygems.org/`
- 내부적으로 Ruby 표준 라이브러리 `net/http` + `openssl` 사용.

<br>

#### 2. SSL/TLS 세션 준비
- Ruby는 OS 루트 인증서 저장소를 직접 사용하지 않고
- 🔑 Ruby 설치 시 포함된 OpenSSL + **별도의 CA 번들(cert.pem)을 참조**


### 5. 참조
[왜 이런 불편한 과정을 거쳐야 하는가?](https://june222.github.io/blog/ssl-ca)