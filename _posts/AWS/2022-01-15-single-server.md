---
title: "[AWS] 단일 서버 아키텍처 Part 1"

categories:
  - AWS
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

<img src = "single-server.png" width=550 height=450>

&nbsp; 사용하는 AWS 서비스는 네트워크 관련 서비스, EC2, Route 53이 있다. 사용자의 요청은 Route53과 Internet Gateway를 거쳐서 웹 서버로 도착한다.

> ## 구축

### VPC

&nbsp; 우선 VPC를 생성한다. vpc의 이름은 "single-server-vpc"이고, IPv4의 CIDR 블록은 10.0.0.0/16으로 설정한다.

<img src = "single-server-vpc.png" width=800>

---

### Subnet

&nbsp; VPC의 CIDR블록 이내에 Subnet을 생성한다. 서브넷의 이름은 "single-server-public"으로 정하고, 가용 영역은 ap-northeast-2a를 선택한다. IPv4의 CIDR 블록은 10.0.0.0/24로 설정한다. 이 때, 주의할 점은 생성된 VPC의 CIDR 블록보다 크기가 작아야 한다.

<img src = "single-server-subnet.png" width=800>

---

### Internet Gateway

&nbsp; Internet Gateway의 이름은 "single-server-igw"라고 정한다.

<img src = "single-server-igw.png">

&nbsp; 생성한 Internet Gateway를 sinlge-server-vpc에 attach시킨다.

<img src = "single-server-vpc-igw-attach.png">

---

### Route Table

&nbsp; 이번에는 Route Table을 생성한다. Route Table은 서브넷 안에 위치시킬 웹 서버가 외부와 원할하게 통신하기 위해서 설정해야 한다. Route Table의 이름은 "single-server-rt"로 정하고, VPC는 single-server-vpc를 선택한다.

<img src = "single-server-rt.png">

&nbsp; 생성한 Route Table에 서브넷과 인터넷 게이트웨이를 연결해준다.

<img src = "single-server-rt-subnet-attach.png" width=800 height=600> 

<img src = "single-server-rt-igw-attach.png" width=800 height=800>

---

### EC2

&nbsp; 웹 서버를 만들기 위해 AWS의 EC2 서비스를 사용한다. EC2의 AMI는 OS는 Linux이고 프리티어인것을 사용한다.

<img src = "single-server-ec2-ami.png" width=800 height=800>

&nbsp; VPC는 single-server-vpc, 서브넷은 single-server-public을 선택한다.

![image](single-server-ec2-detail.png){: width="100%" height="100%"}

&nbsp; EC2 인스턴스의 이름은 single-server-ec2로 한다.

<img src = "single-server-ec2-name.png" width=800 height=800>

&nbsp; EC2의 Security Group을 설정한다. 이름은 single-server-ec2-sg로 정한다. 추가할 요소는 총 세가지다. SSH는 웹 서버의 터미널로 접근하기 위해 추가한다. 이 때, 개발을 진행할 디바이스의 IP만 추가한다. HTTP와 HTTPS는 웹 서버로 사용할 것이기 때문에 추가하고 개방해야하기 때문에 소스는 "위치 무관"으로 설정한다.

![image](single-server-ec2-sg.png){: width="100%" height="100%"}

&nbsp; EC2에 SSH 접속할 때 필요한 키를 다운로드하고 저장한다.

<img src = "single-server-key.png">

---

### EIP

&nbsp; 터미널로 접근하여 웹 서버를 설치하기 위해 동적 IP를 할당받고 EC2에 연결한다.

<img src = "single-server-eip-ec2-attach.png">

---

### 웹 서버 설치

&nbsp; 생성한 EC2 인스턴스를 SSH로 접속하고 웹 서버를 설치한다. 이 글에서는 웹 서버로 Nginx를 선택했다.

<img src = "single-server-web.png">

&nbsp; 동적IP를 통해 접속하면 성공적으로 웹 서버를 설치한 것을 볼 수 있다.

<img src = "single-server-nginx.png">

---

### Route53

&nbsp; Route53을 통해 도메인을 등록하고 호스팅 영역을 생성하면 사용할 수 있다. 하지만 유료 서비스이기에 이 글에서는 생략한다.