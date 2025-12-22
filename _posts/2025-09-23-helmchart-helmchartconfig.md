---
layout: post
title: HelmChart와 HelmChartConfig의 차이점
description: 이것도 사내 정기 회의에서 나온 얘기, HelmChart와 HelmChartConfig 때문에 회의 중간 대화의 혼선이 있었고 이를 방지하고 정리하고자함.
post-image: ../assets/images/helm.png
author: 강준우
tags:
- kubernetes
- helmchart
- helmchartconfig
- 
---

# Intro
이것도 사내 정기 회의에서 나온 얘기입니다. HelmChart와 HelmChartConfig 때문에 회의 중간 대화의 혼선이 있었고 짧은 시간 동안 대화가 진전이 없었습니다. 이를 방지하고자 정리하려합니다.

우선 Helm 입니다.

### Helm

Helm은 **쿠버네티스 패키지 관리**를 도와줍니다. [Helm 공식 홈페이지](https://helm.sh/)에는 "쿠버네티스 애플리케이션을 관리하는 것을 도와줍니다." 라고 나와있습니다. 

흔히 패키지 관리를 도와주는 Node.js의 npm 과 Python의 pip와 같은 역할이라고 보면 됩니다.

필요한 리소스나 라이브러리들을 한 줄씩 명령어로 다운 받고 설치하는 것이 아니라 Helm을 이용하여 필요한 모든 리소스가 다 담겨있는 `HelmChart`를 관리합니다.

Helm의 3가지 주요 개념은 아래와 같습니다.

#### 1. Chart

애플리케이션을 배포하기 위해 필요한 리소스들을 정리한 yaml 파일

#### 2. Repository

Chart를 저장한 public 혹은 private registry

#### 3. Release

kubernetes cluster 에서 구동되는 차트 인스턴스. 일반적으로 동일한 Chart를 여러 번 설치할 수 있고 새로운 Release로 관리되게 됩니다. 

<br>

아래 순서와 같이 연계된다고 보시면 됩니다.

kubernetes cluster 내부에 Helm Chart를 원하는 Repository에서 검색 후 설치 -> 각 설치에 따른 새로운 Release 생성


<br>

# 본문

### HelmChart

[Helm 공식 홈페이지](https://helm.sh/)에서는 "Helm Chart는 아주 복잡한 Kubernetes application을 정의하고, 설치하고, 업그레이드 하는 것에 도움을 준다."고 설명하고 있습니다.

[#Helm](#helm)에서 말한 것 처럼 `HelmChart`는 Helm Package로 Kubernetes Cluster에서 애플리케이션이 기동되기 위해 필요한 모든 리소스들이 포함되어 있는 **yaml 파일들의 묶음**입니다.

<br>

# 결론

<br>

# 참고

- [Helm 공식 홈페이지](https://helm.sh/)
- [KT 클라우드 - Helm 이란?](https://tech.ktcloud.com/entry/Helm-%EC%9D%B4%EB%9E%80)
- [쿼카러버의 기술 블로그](https://etloveguitar.tistory.com/141)