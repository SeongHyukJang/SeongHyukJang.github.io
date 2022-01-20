---
title: "[AWS] 단일 서버 아키텍처 Part 1"

categories:
  - AWS Architecture
tags:
  - [AWS Architecture, Single Server, AWS Route53, AWS VPC, AWS EC2]

toc: true
toc_sticky: true

date: 2022-01-15
last_modifed_at: 2022-01-18
---

> ## 개요

&nbsp; 이 글에서는 AWS를 사용하여 웹 서버를 단일 서버로 설계하고 구축하는 방법을 설명한다.

> ## 아키텍처 구성도

&nbsp; AWS 서비스를 이용하여 설계한 아키텍처는 다음과 같다.

![single-server](https://user-images.githubusercontent.com/49023663/150041645-ebd0fb3e-bc25-4d90-8ba9-d85157207c07.png)

&nbsp; 사용하는 AWS 서비스는 네트워크 관련 서비스, EC2, Route 53이 있다. 사용자의 요청은 Route53과 Internet Gateway를 거쳐서 웹 서버로 도착한다.

> ## 구축

### VPC

&nbsp; 우선 VPC를 생성한다. vpc의 이름은 "single-server-vpc"이고, IPv4의 CIDR 블록은 10.0.0.0/16으로 설정한다.

![single-server-vpc](https://user-images.githubusercontent.com/49023663/150041731-29d6ab0a-6200-42af-b0e9-2a3d2f7de2d8.png)

---

### Subnet

&nbsp; VPC의 CIDR블록 이내에 Subnet을 생성한다. 서브넷의 이름은 "single-server-public"으로 정하고, 가용 영역은 ap-northeast-2a를 선택한다. IPv4의 CIDR 블록은 10.0.0.0/24로 설정한다. 이 때, 주의할 점은 생성된 VPC의 CIDR 블록보다 크기가 작아야 한다.

![single-server-subnet](https://user-images.githubusercontent.com/49023663/150041768-9ecfd54f-f7de-46cb-bd9d-5ba6e3fc1d6d.png)

---

### Internet Gateway

&nbsp; Internet Gateway의 이름은 "single-server-igw"라고 정한다.

![single-server-igw](https://user-images.githubusercontent.com/49023663/150041899-f9385cd6-c266-4a0e-a9e8-890e9672cba6.png)

&nbsp; 생성한 Internet Gateway를 sinlge-server-vpc에 attach시킨다.

![single-server-vpc-igw-attach](https://user-images.githubusercontent.com/49023663/150041917-4cf6b9ed-b97c-4d36-9835-ea1c6e6c0780.png)

---

### Route Table

&nbsp; 이번에는 Route Table을 생성한다. Route Table은 서브넷 안에 위치시킬 웹 서버가 외부와 원할하게 통신하기 위해서 설정해야 한다. Route Table의 이름은 "single-server-rt"로 정하고, VPC는 single-server-vpc를 선택한다.

![single-server-rt](https://user-images.githubusercontent.com/49023663/150041970-ff0ccb9a-3ba6-4919-89e8-3a07c6721f1c.png)

&nbsp; 생성한 Route Table에 서브넷과 인터넷 게이트웨이를 연결해준다.

![single-server-rt-subnet-attach](https://user-images.githubusercontent.com/49023663/150041986-f03ce9e1-790d-48e5-a9cf-2ffa2859a05f.png)

![single-server-rt-igw-attach](https://user-images.githubusercontent.com/49023663/150042005-2e4ff49f-8542-4c50-b6ae-e18ba85ce0ec.png)

---

### EC2

&nbsp; 웹 서버를 만들기 위해 AWS의 EC2 서비스를 사용한다. EC2의 AMI는 OS는 Linux이고 프리티어인것을 사용한다.

![single-server-ec2-ami](https://user-images.githubusercontent.com/49023663/150042051-151dcd14-916d-4b33-9f20-6035821cf4e6.png)

&nbsp; VPC는 single-server-vpc, 서브넷은 single-server-public을 선택한다.

![single-server-ec2-detail](https://user-images.githubusercontent.com/49023663/150043005-2f9ab12a-09c6-423b-b24d-84f717b94fda.png)

&nbsp; EC2 인스턴스의 이름은 single-server-ec2로 한다.

![single-server-ec2-name](https://user-images.githubusercontent.com/49023663/150042074-161ba525-71c7-415f-902f-182af616d033.png)

&nbsp; EC2의 Security Group을 설정한다. 이름은 single-server-ec2-sg로 정한다. 추가할 요소는 총 세가지다. SSH는 웹 서버의 터미널로 접근하기 위해 추가한다. 이 때, 개발을 진행할 디바이스의 IP만 추가한다. HTTP와 HTTPS는 웹 서버로 사용할 것이기 때문에 추가하고 개방해야하기 때문에 소스는 "위치 무관"으로 설정한다.

![single-server-ec2-sg](https://user-images.githubusercontent.com/49023663/150042975-37e90a94-acdf-4c42-a764-d4d1f002e395.png)

&nbsp; EC2에 SSH 접속할 때 필요한 키를 다운로드하고 저장한다.

![single-server-key](https://user-images.githubusercontent.com/49023663/150042080-bb799520-2c38-4811-bf3b-a68ab34c8352.png)

---

### EIP

&nbsp; 터미널로 접근하여 웹 서버를 설치하기 위해 동적 IP를 할당받고 EC2에 연결한다.

![single-server-eip-ec2-attach](https://user-images.githubusercontent.com/49023663/150042094-0971403f-79c5-4f71-ac02-e71ed49051f1.png)

---

### 웹 서버 설치

&nbsp; 생성한 EC2 인스턴스를 SSH로 접속하고 웹 서버를 설치한다. 이 글에서는 웹 서버로 Nginx를 선택했다.

![single-server-web](https://user-images.githubusercontent.com/49023663/150042123-cb03375f-cae2-4f2d-867d-79ba2428bb67.png)

&nbsp; 동적IP를 통해 접속하면 성공적으로 웹 서버를 설치한 것을 볼 수 있다.

![single-server-nginx](https://user-images.githubusercontent.com/49023663/150042133-0b451c34-a403-4225-b470-bd20c06dcbf8.png)

---

### Route53

&nbsp; Route53을 통해 도메인을 등록하고 호스팅 영역을 생성하면 사용할 수 있다. 하지만 유료 서비스이기에 이 글에서는 생략한다.