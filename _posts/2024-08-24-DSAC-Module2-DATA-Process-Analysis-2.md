---
layout: post
title: gg-17-데이터 탐색
description: 데이터 탐색을 위한 기본 명령어들 정리
post-image:
author: 강준우
tags:
  - ipynb
  - xlsx
  -
---

# 목차

## 0. 탐색적 분석

- 탐색적 분석 정의

## 1. 데이터 탐색 예시

- 1.1 엑셀 파일 읽기
- 1.2 이미지 파일 읽기
- 1.3 텍스트 파일 읽기

## 2. dataFrame 변경

- 2.1

# 코드

### 1.1 엑셀 파일 읽기

```python
    import pandas as pd
    power_data = pd.read_excel('location/file.xlsx') # xlsx 만 지원
    power_data
```

### 1.2 이미지 파일 읽기

```python
# 마크다운에서
<img src = "img/01-회귀분석그래프.png">

# 코드에서
from IPython.display import Image
Image('img/anaconda.png')
```

### 1.3 텍스트 파일 읽기

```python
dfblankhan = pd.read_csv('data/blankhan.txt')  # .txt 한글 에러-UnicodeDecodeError
dfblankhan

dfblankhan = pd.read_csv('data/blankhan.txt')  # .txt 한글 에러-UnicodeDecodeError
dfblankhan
```
