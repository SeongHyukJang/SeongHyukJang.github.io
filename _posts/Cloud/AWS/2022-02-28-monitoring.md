---
title:  "[AWS] 모니터링 서비스 이해하기"

categories:
  - AWS
tags:
  - [CloudWatch, CloudTrail]

toc: true
toc_sticky: true

date: 2022-02-28
last_modified_at: 2022-02-28
---

> ## 개요

&nbsp; 이 글에서는 AWS에서 제공하는 모니터링 서비스를 알아본다.

> ## CloudWatch

### 1.개념

&nbsp; Amazon CloudWatch는 AWS 자원을 모니터링할 수 있는 서비스다. 고객이 사용하는 모든 AWS 자원을 실시간으로 모니터링 하여 log 또는 metric을 수집한다. 

### 2. 특징

- 수집된 데이터를 이용하여 AWS Console에서 그래프를 만들 수 있다. 고객이 원하는대로 커스터마이징이 가능하다는 장점이 있다. 
- 수집되는 metric이 평소와는 다르게 이상 수치를 보인다면 경보를 울리게할 수 있다. 예를 들어, 서버의 CPU 사용률이 80%가 넘어가면 경보를 생성하게끔 설정할 수 있다.
- 수집되는 데이터를 보존하는 기간을 정할 수 있다. 만약 오랫동안 보관해야 한다면 S3를 이용하는 방법이 있다.

> ## CloudTrail

### 1.개념

&nbsp; Amazon CloudTrail은 AWS Account가 활동하는 정보를 모니터링 하는 서비스다. 즉, AWS Cloud를 여러 명이서 사용할 때 어떤 IAM User가 무엇을 했는지 추적할 수 있다. AWS Console, CLI, SDK 모두에 대해 수집할 수 있으며, 추적 내용을 보관할 수 있다. 

### 2.특징

- AWS Account가 생성될 때 자동으로 CloudTrail은 활성화 된다.
- 아래 그림과 같이 어떤 자원을, 언제, 어디서, 무엇을 했는지 기록한다. 

![image](https://user-images.githubusercontent.com/49023663/155972325-453b4a25-a547-4617-af9f-3f422f4e147e.png)