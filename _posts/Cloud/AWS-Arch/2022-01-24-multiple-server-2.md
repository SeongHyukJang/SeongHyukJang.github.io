---
title: "[AWS] 다중 서버 아키텍처 Part 2"

categories:
  - AWS Architecture
tags:
  - [AWS Architecture, High Availability, Fault Tolerance, Disaster Recovery, MySQL, nohup, Launch Template, Auto Scaling Group, Applicaion Load Balancer, ALB, Target Group]

toc: true
toc_sticky: true

date: 2022-01-24
last_modifed_at: 2022-02-03
---

> ## 개요

&nbsp; 이 글에서는 "다중 서버 아키텍처 Part 1"에 이어서 아키텍처 구축을 진행한다. AWS Session Manager를 이용하여 DB 설계, 웹 서버 설치, 그리고 각종 네트워크 설정을 진행한다.

> ## 구축

### DB 구성

&nbsp; 웹 서버에서 DB에 SSH 접속하기 위해 MySQL Client를 설치한다.

![multiple-server-mysql-install](https://user-images.githubusercontent.com/49023663/150727622-77a9b2bc-1fc3-49ee-aab2-04c42a9261ac.PNG)

&nbsp; 설치 후, RDS의 엔드포인트를 이용하여 접속한다.

![multiple-server-mysql-connect](https://user-images.githubusercontent.com/49023663/150727627-8b3df69c-76a1-40cc-99b5-25159c3503ac.PNG)

&nbsp; 테스트를 위해 테이블을 하나 만들어서 데이터를 넣어둔다.

![multiple-server-mysql-data](https://user-images.githubusercontent.com/49023663/150727653-0a2fdd05-f63a-446e-9b10-f563fa3bb178.PNG)

---

### 웹 서버 구성

&nbsp; 웹 서버는 EC2에 Python의 built-in 모듈을 사용하여 80번 포트에 연다. 설정 시, 본인이 생성한 계정과 비밀번호, 데이터베이스, 테이블을 적절히 넣어준다.

![multiple-server-web-python](https://user-images.githubusercontent.com/49023663/152146159-73e1ae06-0113-48d7-bdf3-7d8c1a6242e3.PNG)

&nbsp; 추후에는 Auto Scaling Group으로 자동 실행하겠지만 현재는 초기 설정 단계이기 때문에 nohup을 이용하여 프로세스를 백그라운드로 실행한다.

![multiple-server-web-nohup](https://user-images.githubusercontent.com/49023663/152146990-8c9c8ca6-9da3-4027-9349-75356ab5f809.PNG)

&nbsp; 웹 서버의 코드에 오류는 없는지 curl 명령어를 통해 확인해본다.

![multiple-server-web-connect-success](https://user-images.githubusercontent.com/49023663/152147166-03aa7abf-2cf0-4a42-bcae-d4262be6f950.PNG)

&nbsp; 이로써, EC2의 설정은 완료되었다. 이제 시작 템플릿을 만들기 위해 EC2를 AMI로 만든다. AMI로 만든다는 것은 CD를 굽는 것과 마찬가지다.

![multiple-server-ami](https://user-images.githubusercontent.com/49023663/152147793-7e212ea8-83d3-4571-a756-0379bb97219e.PNG)

---

### 시작 템플릿 생성

&nbsp; 템플릿의 이름을 설정하고, Auto Scaling을 사용할 것이기 때문에 체크박스에도 체크 해준다.

![multiple-server-template-1](https://user-images.githubusercontent.com/49023663/152148515-7ec85834-9164-4faa-9391-a7064f152bd0.PNG)

&nbsp; 이미지는 내 AMI를 선택한다.

![multiple-server-template-2](https://user-images.githubusercontent.com/49023663/152148510-371ee0f9-d253-4c7a-9394-701db678b1f9.PNG)

&nbsp; 인스턴스 유형은 t2.micro (프리티어), 키 페어, 서브넷 그룹, 보안 그룹 모두 EC2에 설정한 것과 동일하게 설정해준다.

![multiple-server-template-3](https://user-images.githubusercontent.com/49023663/152148512-fab911a5-0589-4a71-a7f8-a3599a475124.PNG)

&nbsp; 고급 설정에서 IAM을 연결해준다.

![multiple-server-template-4](https://user-images.githubusercontent.com/49023663/152161202-21b897e7-1dbf-4814-8701-6a8affc5185d.PNG)

&nbsp; EC2 서버를 시작할 때 생성했던 main.py를 실행해야하기 때문에 사용자 데이터를 작성해준다.

![multiple-server-template-5](https://user-images.githubusercontent.com/49023663/152161209-024d8a5c-2029-462c-b419-13ce89f19d84.PNG)

---

### Auto Scaling Group 생성

&nbsp; Auto Scaling Group의 시작 템플릿은 위에서 생성한 것을 선택하고 이름을 설정한다.

![multiple-server-asg_LI](https://user-images.githubusercontent.com/49023663/152162020-f3ba3270-7926-4f83-95c2-a59541a11dbc.jpg)

&nbsp; VPC를 선택하고 가용 영역은 ap-northeast-2a와 ap-northeast-2c를 선택한다.

![multiple-server-asg-2](https://user-images.githubusercontent.com/49023663/152161912-973e74f7-2f4b-4173-8fb0-04b45701fcd5.PNG)

&nbsp; 현재는 로드 밸런서가 존재하지 않기 때문에 없음을 선택하고 진행한다.

![multiple-server-asg-3](https://user-images.githubusercontent.com/49023663/152158726-f8323435-07af-4238-ad7d-4a58dfd353f4.PNG)

&nbsp; 그룹의 크기를 설정한다. 이 글에서는 desired는 1, minimum은 1, maximum을 2로 설정한다.

![multiple-server-asg-4](https://user-images.githubusercontent.com/49023663/152158727-f84a9882-ac39-4a95-b773-2f04aa91be1b.PNG)

---

### Target Group 생성

&nbsp; Target Group은 ALB에 연결하기 위해 생성한다. 대상 유형은 인스턴스로 설정하고 이름을 정한다. 

![multiple-server-target-group-1](https://user-images.githubusercontent.com/49023663/152166819-600faf2a-deb2-4d29-95db-89966f136af4.PNG)

&nbsp; ALB에서 HTTP 프로토콜로 요청을 전달할 것이기 때문에 HTTP, 80번 포트를 선택해주고, VPC를 선택해준다.

![multiple-server-target-group-2](https://user-images.githubusercontent.com/49023663/152166811-5a50b19f-bf41-433b-b98e-d388ddc7b8b3.PNG)

&nbsp; 대상은 Auto Scaling Group에 의해 생성된 인스턴스를 선택하고 대상에 추가한다.

![multiple-server-target-group-3](https://user-images.githubusercontent.com/49023663/152166817-9946fafa-895b-44cf-b654-3b4224410894.PNG)

---

### Application Load Balancer 생성

&nbsp; 로드 밸런서의 이름을 설정해주고, 체계는 인터넷 경계(internal-facing)를 선택한다.

![multiple-server-alb-1](https://user-images.githubusercontent.com/49023663/152166939-218b8daa-49d2-4b83-b9c0-238905889040.PNG)

&nbsp; VPC를 선택하고 가용 영역은 ap-northeast-2a와 2c에서 public 서브넷을 선택해준다.

![multiple-server-alb-2](https://user-images.githubusercontent.com/49023663/152166941-6fe5df93-8646-4206-a564-6a9fd1669918.PNG)

&nbsp; 인바운드 규칙에 HTTP 프로토콜(포트 번호 80)을 모든 IP (0.0.0.0/0)이 추가 되어 있다면 어떤 보안 그룹을 사용하든 상관없다. 그리고 대상 그룹은 우리가 생성한 Target Group을 연결한다.

![multiple-server-alb-3](https://user-images.githubusercontent.com/49023663/152166937-c0cf4f7e-b482-47c6-9b4c-413afe3130c7.PNG)

&nbsp; 모든 설정을 완료 했다면 ALB의 DNS에 요청을 보내본다. 연결이 잘 되어 있다면 아래와 같이, 테이블에 넣은 정보가 보일 것이다.

![multiple-server-alb-4](https://user-images.githubusercontent.com/49023663/152166938-7273d8d6-a9e4-4750-8df0-349a541fbac0.PNG)