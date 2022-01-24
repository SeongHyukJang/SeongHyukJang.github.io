---
title: "[AWS] 다중 서버 아키텍처 Part 1"

categories:
  - AWS Architecture
tags:
  - [AWS Architecture, High Availability, Fault Tolerance, Disaster Recovery, AWS VPC, AWS EC2, NAT Gateway, AWS RDS]

toc: true
toc_sticky: true

date: 2022-01-20
last_modifed_at: 2022-01-24
---


> ## 개요

&nbsp; 이 글에서는 Cloud 아키텍처를 다중 서버로 설계하고 구축하는 방법을 설명한다. 다중 서버로 설계하면 생기는 장점은 다음과 같다.

- <b>고가용성(High Availability)</b>을 가질 수 있다. 즉, 서버 몇 개가 죽어도 서비스는 정상작동된다.
- <b>내결함성(Fault Tolerance)</b>을 가질 수 있다. 고가용성을 가진다는 것과 비슷하지만, 서비스가 절대 중단되지 않는 상황 즉, zero downtime을 가진다는 차이가 있다.
- <b>재해 복구(Disaster Recovery)</b>를 가질 수 있다. 자연 재해와 같은 것으로도 서비스가 중단 되지 않는다.

> ## 아키텍처 구성도

&nbsp; 아키텍처 구성은 다음과 같다.

![multiple-server](https://user-images.githubusercontent.com/49023663/150510228-499e3d1c-2475-4622-b859-efc2aa872983.png)

&nbsp; 사용자의 요청은 CloudFront, Internet Gateway, Application Load Balancer, Web Server, RDS 순으로 이동한다.

> ## 구축

### VPC

&nbsp; VPC의 이름은 "multiple-server-vpc"이고, CIDR 블록은 10.0.0.0/16으로 한다.

![multiple-server-vpc](https://user-images.githubusercontent.com/49023663/150467256-171f6bb0-3ae5-4b1f-9cbc-27d325a798d8.PNG)

---

### Subnet

&nbsp; Subnet은 Public과 Private을 나뉜다. <br>
&nbsp; ALB는 모든 트래픽을 받을 수 있게 Public 서브넷인 multiple-server-public에 위치시킨다. Web 서버와 DB는 보안이 필요하기 때문에 Private에 위치한다. 웹 서버는 multiple-server-private-ap에 위치시킨다. <br>
&nbsp; 하지만 DB는 HA와 FT를 보장하기 위해 Master와 Slave는 다른 가용 영역에 위치시킨다. Master는 multiple-server-private-db-a, Slave는 multiple-server-private-db-c에 위치시킨다.

![multiple-server-subnet](https://user-images.githubusercontent.com/49023663/150468954-3a233c4f-8d0c-46eb-9055-57ad3fdfa121.PNG)

---

### Internet Gateway

&nbsp; IGW는 "multiple-server-igw"로 정하고 VPC에 연결한다.

![multiple-server-igw](https://user-images.githubusercontent.com/49023663/150471745-82809b71-eed4-4fc7-a042-a1d6fb0c5809.PNG)

![multiple-server-igw-vpc-attach](https://user-images.githubusercontent.com/49023663/150473635-43956bd5-a9c8-44d1-856d-06aff85ab94f.PNG)

---

### NAT Gateway

&nbsp; Private 서브넷에 존재하는 자원들에 접속해야 하기 때문에 NAT Gateway를 생성한다. 이름은 multiple-server-nat로 하고 multiple-server-public에 위치시킨다.

![multiple-server-nat](https://user-images.githubusercontent.com/49023663/150477078-668d5d23-dd22-4f45-9368-ab8e6ec38908.PNG)

---

### Route Table

&nbsp; Route Table은 외부와 내부 각각의 경로를 알려주는 테이블이 필요하기 때문에 2개를 만든다. <br>
&nbsp; 외부를 위한 테이블은 multiple-server-rt-ext로 한다. 그리고 Internet Gateway와 Public 서브넷을 연결한다.

![multiple-server-rt-ext](https://user-images.githubusercontent.com/49023663/150483301-a49cee42-78d8-4173-b1e2-1af542726662.PNG)

&nbsp; 내부를 위한 테이블은 multiple-server-rt-int로 한다. 그리고 NAT Gateway와 Private 서브넷들을 연결한다.

![multiple-server-rt-int](https://user-images.githubusercontent.com/49023663/150483372-c24da796-980d-4c26-8785-f85e93bdeb9e.PNG)


---

### EC2

&nbsp; 웹 서버를 설정하기 위해서는 Session Manager를 통해서 접근해야 한다. 웹 서버에는 Public IP가 없기 때문이다. 그렇기 때문에 IAM 역할을 만든다. <br>
&nbsp; 역할의 이름은 multiple-server-web-iam이라 하고 정책은 AmazonSSMFullAccess를 연결한다.

![multiple-server-web-iam](https://user-images.githubusercontent.com/49023663/150484144-6a765012-8dcb-4f8e-815a-8649018bdee0.PNG)

&nbsp; 웹 서버에 사용할 AMI는 Amazon Linux 2 AMI - Kernel 5.10 이고, 프리티어를 이용한다. VPC는 multiple-server-vpc, 서브넷은 multiple-server-private-ap, IAM은 multiple-server-web-iam을 연결한다.

![multiple-server-web](https://user-images.githubusercontent.com/49023663/150484516-613da161-9508-43a3-a287-a2aa0bf5bb0e.PNG)

&nbsp; 웹 서버의 Security Group은 SSH, HTTP(S)를 열어준다. SSH는 개발용이기 때문에 접근하려는 IP만 열고, HTTP(S)는 NAT Gateway가 존재하는 Public 서브넷을 열어준다. DB에서 오는 트래픽도 받아야 하기 때문에 포트 3306으로 DB의 Public 서브넷을 열어준다.

![multiple-server-web-sg](https://user-images.githubusercontent.com/49023663/150486619-c579656c-cfdc-453d-b593-d1a220964cb0.PNG)

---

### RDS

&nbsp; DB를 만들기 전에 먼저 Subnet Group과 Security Group을 만든다. Subnet 그룹은 multiple-server-private-db-a와 multiple-server-private-db-c를 선택하고 그룹의 이름은 multiple-server-subnet-group으로 한다.

![multiple-server-subnet-group](https://user-images.githubusercontent.com/49023663/150490554-5d328568-f48c-46ea-9f9e-c4263d66e9b0.PNG)

&nbsp; Security Group은 MySQL을 사용하기 때문에 3306포트를 열어주고 서브넷은 웹 서버가 있는 서브넷인 10.0.1.0/24을 선택한다.

![multiple-server-db-sg](https://user-images.githubusercontent.com/49023663/150501735-36b0cbba-e5b0-4606-b0e7-31f67ea1bb3d.PNG)

#### Master

&nbsp; 이제 Master용 DB를 만든다. 엔진은 MySQL을 선택하고 버전은 8.0.23으로한다.

![multiple-server-db-type-1](https://user-images.githubusercontent.com/49023663/150496044-9e90c28c-c997-469e-a5b7-e22031034f9d.PNG)

&nbsp; DB 이름은 multiple-server-db-a로 한다. 마스터 이름과 암호도 설정한다.

![multiple-server-db-id-pwd-2](https://user-images.githubusercontent.com/49023663/150502357-78ca7ad0-41e6-44d8-9b97-5b533c4ff495.PNG)

&nbsp; DB의 클래스는 프리티어로 하고 스토리지는 기본 크기로 한다.

![multiple-server-db-storage-3](https://user-images.githubusercontent.com/49023663/150508907-9afde974-462f-4950-8ce3-3ca6c732e70e.PNG)

&nbsp; VPC는 multiple-server-vpc를 선택하고 서브넷 그룹은 multiple-server-subnet-group을 선택한다. Private 서브넷에 위치하기 때문에 퍼블릭 액세스는 '아니요'로 한다. 보안 그룹은 multiple-server-db-sg를 선택하고 가용 영역은 ap-northeast-2a를 선택한다.

![multiple-server-db-connect-4](https://user-images.githubusercontent.com/49023663/150502650-8551d1e5-fd2c-4caf-8bec-20f6716512f7.PNG)

#### Slave

&nbsp; Slave는 생성된 Master의 읽기 복제본을 생성하면 된다. 복제 원본은 multiple-server-db-a를 선택하고 Slave의 식별자는 multiple-server-db-c로 한다.

![multiple-server-db-replica-1](https://user-images.githubusercontent.com/49023663/150507995-da5208e2-3f4d-49e8-973a-3bf227846bf6.PNG)

&nbsp; 서브넷 그룹은 Master와 동일하게 하되, 가용 영역은 ap-northeast-2c로 한다.

![multiple-server-db-replica-2](https://user-images.githubusercontent.com/49023663/150496391-edcc922d-229a-450e-87c1-118f22c8279c.PNG)

&nbsp; DB 인스턴스 클래스와 스토리지는 Master와 동일하게 설정한다.

![multiple-server-db-replica-3](https://user-images.githubusercontent.com/49023663/150509250-9bfccfa5-f8ac-42ec-a79d-822e74088ac4.PNG)

&nbsp; Master와 Slave를 모두 설정 완료하면 아래와 같이 2개의 DB 자원이 생긴다.

![multiple-server-db-result](https://user-images.githubusercontent.com/49023663/150706098-2d330132-a0b1-42d4-9b03-0f624fdb117d.PNG)