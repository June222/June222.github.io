---
layout: post
title: rancher install issue
description: 회사내 자동화 스크립트로 설치 시 rancher가 설치되지 않는 이유
post-image: ../assets/images/rancher-logo.svg
author: 강준우
tags:
- rke2
- rancher
- harbor
- kubernetes
---

# [문제상황]
- 월요일 회의 때 idocs 스크립트 설치 가능 여부 설명 정정
- scharbor 8.19 자 shieldID Helperjob 이슈
- 자동화 스크립트가 아닌 rke2 공식 document로 설치 시 버전 미지원 이슈

# [상세]
- 월요일 회의 때 idocs 스크립트 설치 가능 여부 설명 정정
- 스크립트로 설치할 때 문제 없고, 이후 진행사항 또한 문제 없는 것 확인하였습니다. (이때 설치되는 클러스터 버전 1.30)
- scharbor 8.19자 shieldID Helperjob 이슈

scharbor의 8.19일자 shieldID 설치 시 helprjob이 rabbitmq, redis pod보다 먼저 뜨는 이슈

- 이후 진행상황에 문제가 발생

- scharbor shieldID 최신 버전에서는 해당 이슈 사라진 것으로 확인
- rke2 공식 document로 설치 시 버전 미지원 이슈
- idocs 스크립트가 아닌 rke2 공식 document로 설치 시 클러스터 버전: 1.32
- scharbor의 rancher는 클러스터 1.32버전을 지원하지 않음
- **rancher v2.11.x 부터 cluster 1.32 버전 지원** -> 2.11.1 버전/최신 버전 설치 확인
- rancher v.2.12.1부터 cluster 1.33 버전 지원
