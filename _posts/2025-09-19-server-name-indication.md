---
layout: post
title: SNI(Server Name Indication)
description: 회의 중 나온 말 "SNI가 없어서 IP가 뭐하는 건지 모른다" 로 시작한 SNI 알아보기
post-image: ../assets/images/SNI.png
author: 강준우
tags:
- SNI
- IP/Port
- Https
- TLS
---

# Introduction
사내 회의 중 나온 말 "SNI가 없어서 IP가 뭐하는 건지 모른다" 로 시작한 SNI 알아보기

# SNI(Server Name Indication)이란?

- TLS 확장(Extension) 중 하나
- 클라이언트가 TLS 핸드세이크 시작 시(**ClientHello**)에 접속하려는 도메인 이름을 Host Header에 담아 서버에 미리 알려줌.
- 서버는 이 값을 보고 어떤 인증서(가상 호스트)를 선택할 지 결정할 수 있음 -> 한 IP:443에서 여러 도메인을 HTTPS로 서비스 가능(가상 호스팅)


### 왜 꼭 필요한가?
- 기존엔 IP 주소당 하나의 SSL 인증서만 바인딩 가능
- IPv4 주소 고갈 문제
- CDN 서비스 제약 및 비용 증가
- **하나의 서버에서 여러 도메인을 효율적으로 관리**하는 것을 도움
- **클라우드 환경과 MSA에서 특히 중요한 역할을 합니다.**

# 동작 흐름
1. 클라이언트 ➡️  ClientHello(+SNI = 예: `a.example.com`) 전송
2. 서버(또는 프록시/로드밸런서) ➡️ SNI 보고 해당 호스트의 인증서 선택
3. ServerHello(선택된 인증서 체인 포함) ➡️ 핸드세이크 완료 ➡️ HTTP 요청 시작

### SNI를 지원 안 한다면?
- HTTP/1.1의 `Host` 헤더는 TLS 수립 이후(암호화된 레이어 위) 보내져서, **인증서 선택 전에 쓸 수 없음.**
- SNI가 없으면 서버는 "Default 인증서" 만 줄 수 있어 멀티 도메인 환경에서 호스트 네임 불일치(Certificate Mismatch) 발생.


# SNI가 가진 문제점

### SNI Support Concerns

1999년도에 drafted 되어 꽤 오래되었지만 이것을 지원하지 않는 legacy browsers나 os들이 여전히 있습니다. 

SNI를 지원하지 않는 오래된 브라우저들은 현대 브라우저와 비교할 때 minuscule 함에도 불구하고 SNI를 인식하지 못해도 default SSL certificate를 내놓습니다. 그리고 보통 **common name missmatch error**를 만들어 냅니다.

구글같은 많은 회사가 고객을 위해 SNI를 구현하지만 지원하지 않는 드문 경우 SAN certificate로 돌아갑니다. 하지만 자연스럽게 이 문제는 사용자와 사업주가 그들의 현대 기술로 업그레이드 해나가며 사라질 것으로 기대됩니다.

### SNI Privacy Concerns

현재 TLS의 stable version(1.2)는 최초 handshake 부분과 더불어 SNI extension 정보를 암호화하지않고 보냅니다. 결국 공격자는 웹 통신이 완벽히 암호화 되어있어도 유저의 히스토리 정보를 볼 수 있습니다.

아마존이나 구글같은 다양한 CSP는 domain fronting이란 workaround를 허용했습니다. Domain fronting은 TLS 협상에서 클라우드 프로바이더의 hostname을 사용하고 http 헤더에 타겟 사이트의 hostname을 사용하여 SNI 정보를 난독화함으로 웹 히스토리 유출을 막을 수 있습니다.

운 좋게도 시스템적 솔루션이 TLS version 1.3을 위한 Encrypted SNI(ESNI) 익스텐션으로 실험적 draft로 공개되었습니다. ESNI는 SNI 정보를 암호화하여 모든 보안 문제를 mitigating 합니다. 불행하게도 TLS가 네트워크 보안 프로토콜의 de facto가 천천히 되어감에도 불구하고 TLS 1.3는 아직 산업에 널리 적용되지 못하고 있습니다. ESNI의 정보나 HTTPS와 TLS의 보안에 관한 새로운 기사들을 기대해주시기를 바랍니다.


# 참고

[SNI: Virtual Hosting for HTTPS](https://www.ssl.com/article/sni-virtual-hosting-for-https/)

[SNI(Server Name Indication)와 HTTPS의 작동 원리: 웹 보안 통신의 핵심 메커니즘](https://notavoid.tistory.com/146)


[SNI가 뭐야](https://chatgpt.com/share/68ccfff2-35e0-800c-bd47-2689eb8884bb)