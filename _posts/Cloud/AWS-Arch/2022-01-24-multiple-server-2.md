---
title: "[AWS] 다중 서버 아키텍처 Part 2"

categories:
  - AWS Architecture
tags:
  - [AWS Architecture, High Availability, Fault Tolerance, Disaster Recovery, AWS VPC, AWS EC2, NAT Gateway, AWS RDS]

toc: true
toc_sticky: true

date: 2022-01-24
last_modifed_at: 2022-01-24
---

> ## 개요

&nbsp; 이 글에서는 

> ## 구축

### DB 구성

&nbsp; 웹 서버에서 DB에 SSH 접속하기 위해 MySQL Client를 설치한다.

![multiple-server-mysql-install](https://user-images.githubusercontent.com/49023663/150727622-77a9b2bc-1fc3-49ee-aab2-04c42a9261ac.PNG)

&nbsp; 설치 후, RDS의 엔드포인트를 이용하여 접속한다.

![multiple-server-mysql-connect](https://user-images.githubusercontent.com/49023663/150727627-8b3df69c-76a1-40cc-99b5-25159c3503ac.PNG)

&nbsp; 테스트를 위해 테이블을 하나 만들어서 데이터를 넣어둔다.

![multiple-server-mysql-data](https://user-images.githubusercontent.com/49023663/150727653-0a2fdd05-f63a-446e-9b10-f563fa3bb178.PNG)


### 웹 서버 구성

&nbsp; 우선 웹 서버는 nginx를 사용한다.

![multiple-server-web-nginx](https://user-images.githubusercontent.com/49023663/150711774-d7af1a0d-0526-453c-b4f3-8f26958904b6.PNG)

&nbsp; PHP 설치




### AMI로 만들고 auto scaling group으로 묶는다.

### ALB 설정

### DB 설정

### CloudFront 설정

### 커넥션 확인