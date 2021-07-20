---
title:  "[AWS] Amazon Virtual Private Cloud(VPC) 및 VPC에 관련된 서비스"

categories:
  - AWS
tags:
  - [AWS VPC, Internet Gateway, Virtual Private Gateway, Transit Gateway, VPN Connect, Direct Connect, AWS DX, AWS Route53]

toc: true
toc_sticky: true

date: 2021-07-18
last_modified_at: 2021-07-18
---



> ## 개요

&nbsp;Amazon Virtual Private Cloud (VPC)는 AWS에서 제공하는 네트워크 서비스다. 고객은 VPC를 이용하여 네트워크 공간을 프로비저닝하여 자신의 사설망을 구축할 수 있다. 그리고 망 안에 다양한 AWS 인스턴스와 서비스를 사용하여 Cloud Infra를 설계하고 구축할 수 있다.<br>
&nbsp;Amazon VPC는 가상의 망이기 때문에 몇가지 요소들과 함께 VPC를 구성해야 외부와 통신이 가능하다. 이 글에서는 VPC를 외부와 연결하는 방법과 종류를 소개한다.

> ## VPC를 연결하는 방법

&nbsp; VPC를 외부의 네트워크 망과 연결하는 방법은 총 2가지가 존재한다.

### VPN Connection

&nbsp; VPC와 외부망을 VPN을 이용하여 연결하는 방법이다. AWS에서는 총 2가지의 방식을 지원한다.

1. Site-to-Site VPN
- IPSec VPN 연결 방식을 사용하는 방법이다.
- 아키텍처 설계 시, 주로 사용된다.
- VPN Connection이라 하면 주로 Site-to-Site VPN을 말한다.

2. Client VPN
- SSL VPN 방식이다. 즉, Clientless 방식으로 연결을 한다.

### Direct Connect (DX)

&nbsp; 전용선을 이용하여 연결하는 방식이다. 즉, VPC를 광케이블을 이용하여 물리적으로 연결하기 때문에 인터넷을 사용하지 않는다. 그래서 인터넷을 통해 들어오는 트래픽에 의해 부하가 심해져도 Direct Connect 방식을 사용하면 속도에 영향을 받지 않는다는 장점이 있다.

### 연결 방식에 따른 아키텍처 설계

1. VPN을 이용하는 방식 <br><br>![image](https://user-images.githubusercontent.com/49023663/126275334-ba76f462-8294-4108-82a4-54d4724ed697.png) 
<br>
- VPN 연결만 사용하여 구성한 경우다.
- VPN 연결 시간과 데이터 전송 양에 따라 요금이 부과된다. DX 대비 저렴한 가격이 장점이다.
- 인터넷을 이용하기 때문에 보안이 DX에 비해 상대적으로 취약하다.
<br>
2. Direct Connect를 이용하는 방식 <br><br> ![image](https://user-images.githubusercontent.com/49023663/126277285-5e655e30-ec1f-48ca-9a63-2596f64e0551.png)
<br>
- AWS DX 서비스만 사용하여 구성한 경우다.
- 전용선을 연결할 때 다양한 대역폭을 제공하기 때문에 VPN 연결 방식보다 통신 속도를 빠르게 설계할 수 있다.
- 연결한 대역폭 유형, 포트 시간 그리고, 데이터 전송 양에 따라 요금이 부과된다.
- 인터넷을 사용하지 않고 연결을 하는 방법이기 때문에 보안에 뛰어나다.
<br>
3. VPN과 Direct Connect 둘 다 이용하는 방식 <br><br> ![image](https://user-images.githubusercontent.com/49023663/126294172-eec39a38-da5c-4837-b760-46ad47247bee.png)
<br>
- VPN 연걸은 백업용을 사용하고 DX를 운영 라인으로 사용하여 구성한 경우다.
- VPN과 DX의 장점을 모두 사용하기 위해 설계하는 방식이다.
<br>

> ## VPC가 외부와 통신하는 방법

### Gateway

&nbsp; Gateway는 단어 그대로 관문 역할을 한다. 즉, VPC에 Gateway가 있어야 외부에서 트래픽이 들어올 수 있고 내부에서도 나갈 수 있다.<br> 이러한 역할을 하는 Gateway는 
&nbsp; Gateway는 VPC 외부에서 들어오는 트래픽을 어떻게 받는지에 따라 종류가 다양하다.


&nbsp; VPC와 연결하는 대상은 다양하다. 회사의 IDC와 같은 On-prem이 될 수도 있고, 내가 설계한 또다른 VPC와 연결하려 할 수도 있고, 아얘 다른 Region에 있는 VPC와 연결을 하고 싶을 수도 있다. 


### Internet Gateway (IGW)
<br><br>

![image](https://user-images.githubusercontent.com/49023663/126326072-94e145e3-ce61-4f3b-9875-2727766aad91.png)
<br>
- 외부의 트래픽이 아무런 제약 없이 VPC 내부로 들어오게 해주는 관문이다.

### Virtual Private Gateway (VGW)
<br>

![image](https://user-images.githubusercontent.com/49023663/126327651-448e39df-2f6f-42cb-ad9b-036c99da57bd.png)
<br>
- 특정 IP 대역의 트래픽만 VPC 내부로 들어올 수 있다.
- VPN 연결 방식을 사용할 때 VPC에는 VGW를 사용해야 한다.
- 같은 Region에 여러 개의 VPC를 연결해야 할 때 사용한다.
- 그림을 보면, AWS Cloud Infra와 고객사의 네트워크 망이 VPN 연결 방식으로 연결 되어 있다. 이 떄, 하나의 VPC안에 2개의 VPC가 VGW를 통해 통신한다.

### Direct Connect Gateway (DGW)
<br><br>

![image](https://user-images.githubusercontent.com/49023663/126326225-da7b64ec-97a4-4405-b4a3-e199bbd28f86.png)
<br>
- VPC를 다른 곳의 AWS DX와 연결할 수 있는 게이트웨이다.
- DX 연결 방식을 사용하면 DGW를 사용해야 한다.
- 내가 생성한 VPC가 여러 계정에 존재하거나, 여러 개의 Region에 존재할 때 사용할 수 있다.
- 그림을 보면, 왼쪽이 외부 Infra의 AWS DX이고 오른쪽이 내 Cloud Infra이며 2개의 계정을 사용하여 총 4개의 VPC가 존재한다. 이 때, 연결은 Private Virtual Interface (Private VIF)로 연결한다.

### Transit Gateway (TGW)
<br><br>

![image](https://user-images.githubusercontent.com/49023663/126355766-60ff34d7-016b-4d5c-892b-655351b4a62a.png)
<br>
- VGW와 DGW보다 향상된 성능의 라우팅 능력을 가지고 있다. 
- Route Table들끼리 CIDR 블록이 겹쳐도 정상적인 라우팅이 가능하다.
- VPN 방식과 DX 방식을 모두 지원하기 때문에 VGW와 DGW 대신 TGW를 사용할 수 있다.
- 그림과 같이, TGW는 VPN 연결, DX 모두 지원한다. 그래서 TGW를 사용하면 아키텍처를 단순화 시킬 수 있고, 관리도 쉬워진다.
- DGW처럼 다른 Region의 VPC와는 연결할 수 없지만, 여러 계정의 VPC들이 동일한 Region에 존재한다면 연결이 가능하다.

### Gateway 비교표

||Virtual Private Gateway|Direct Connect Gateway|Transit Gateway|
||:---|:---:|:---:|
|Multiple Region 지원|X|O|X|
|Multiple Account 지원|X|O|O|
|VPN 연결 방식 지원|O|X|O|
|Direct Connect 연결 방식 지원|O|O|O|


> ## Subnet

> ## Network ACL

> ## Security Group

> ## AWS Route53
