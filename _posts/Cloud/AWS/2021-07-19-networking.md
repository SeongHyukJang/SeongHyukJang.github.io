---
title:  "[AWS] 네트워킹 서비스 이해하기"

categories:
  - AWS
tags:
  - [AWS VPC, Internet Gateway, Virtual Private Gateway, Direct Connect Gateway, Transit Gateway, VPN Connect, Direct Connect, AWS DX, AWS Route53, Security Group, Network ACL, Subnet]

toc: true
toc_sticky: true

date: 2021-07-19
last_modified_at: 2021-12-27
---



> ## 개요

&nbsp;이 글에서는 AWS에서 제공하는 네트워크 서비스를 알아본다. 대표적으로, Amazon Virtual Private Cloud (Amazon VPC)가 존재한다. 고객은 VPC를 이용하여 네트워크 공간을 프로비저닝하여 자신의 사설망을 구축할 수 있다. 그리고 망 안에 다양한 AWS 인스턴스와 서비스를 사용하여 Cloud Infra를 설계하고 구축할 수 있다.<br>
&nbsp;Amazon VPC는 가상의 망이기 때문에 몇가지 요소들과 함께 VPC를 구성해야 외부와 통신이 가능하다. 이 글에서는 VPC를 외부와 연결하는 방법과 종류를 소개한다.

> ## VPC를 외부와 연결하는 방식

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
- VPN 연결은 백업용을 사용하고 DX를 운영 라인으로 사용하여 구성한 경우다.
- VPN과 DX의 장점을 모두 사용하기 위해 설계하는 방식이다.
<br>

> ## Gateway

&nbsp; Gateway는 단어 그대로 관문 역할을 한다. 즉, VPC에 Gateway가 있어야 외부에서 트래픽이 들어올 수 있고 내부에서도 나갈 수 있다. VPC 외부에서 들어오는 트래픽을 어떻게 받는지에 따라 종류가 다양하다.

### Internet Gateway (IGW)
<br>

![image](https://user-images.githubusercontent.com/49023663/126412138-837fa53e-3f88-4d66-b303-d78e2fff20ec.png)

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

&nbsp; 여러 개의 IP를 묶은 형태이다. 즉, 서브넷은 특정 IP 대역을 이용하여 공간을 이룬다. VPC의 CIDR 블록으로 생성된 공간을 서브넷으로 나눌 수 있다. 외부와 통신이 필요한 구역을 Public Subnet으로 부르고, 내부에서만 통신할 구역은 Private Subent이라고 부른다. Public과 Private Subnet은 VPC의 CIDR 블록보다 작고 VPC 내의 다른 Subnet과 겹치지만 않으면 여러 개를 만들 수 있다.
<br><br>
![image](https://user-images.githubusercontent.com/49023663/126412138-837fa53e-3f88-4d66-b303-d78e2fff20ec.png)

<br> 위의 그림을 보면, Public Subnet의 인스턴스는 외부와 통신이 가능하고, Private Subnet의 인스턴스는 불가능하다.

> ## Security

VPC를 구축할 때 보안은 필수다. VPC 내부로 들어오는 트래픽에 대한 접근 제어는 위에서 설명한 외부와의 통신 방식과 Gateway의 종류를 통해 관리된다. 여기서는 VPC 내부로 들어온 트래픽에 대한 접근 제어 방식을 설명한다.

### Network ACL (NACL)

- Subnet 안으로 트래픽이 들어오거나 나갈 때 NACL이 패킷 단위로 검사를 진행한다.
- 한 개의 Subnet에 하나의 NACL이 존재한다.
- 검사를 진행하여 승인 받은 패킷은 내부로 들어오고 거부 되면 Block된다. 이를 Filtering이라고 한다.
- NACL은 인바운드 규칙과 아웃바운드 규칙을 따로 설정할 수 있다.
- Default로 생성되는 NACL은 모든 트래픽을 허용한다.
- Stateless하게 검사를 진행하기 때문에, 트래픽에 대한 상태 정보는 저장하지 않는다.

### Security Group

- AWS 인스턴스로 트래픽이 들어오거나 나갈 때 패킷 단위로 검사를 진행한다.
- 한 개의 Security Group에는 여러 개의 AWS 인스턴스들이 존재할 수 있다.
- NACL과 마찬가지로 인바운드와 아웃바운드 트래픽에 대해 규칙을 각각 설정해줄 수 있다.
- Default로 생성되는 Security Group은 인바운드에 대해서는 모두 Block하고 아웃바운드는 열어둔다.
- Stateful하게 검사를 진행하기 때문에, 트래픽에 대한 상태 정보를 저장한다.

<br>
&#8251; 인바운드 트래픽(Inbound Traffic) : 안으로 들어오려는 트래픽을 뜻한다.<br>
&nbsp;&nbsp;&nbsp; 아웃바운드 트래픽(Outbound Traffic) : 나가려고 하는 트래픽을 뜻한다.

### 트래픽이 이동하는 과정

&nbsp; 트래픽이 이동하는 과정을 다음 그림으로 설명한다.

![image](https://user-images.githubusercontent.com/49023663/126416708-9b9aea03-1bcc-4422-b87f-2a6c1a2cacff.png)

1. 외부에서 트래픽이 VPC를 통해 들어와서 Subnet으로 가려면 제일 먼저 NACL은 만난다.
2. NACL에서 검사를 진행한다. 승인을 받으면 Subnet으로 들어가고 그렇지 못하면 Block 된다.
3. Subnet 안으로 들어온 트래픽이 AWS 인스턴스로 접근하려 하면 Security Group을 만난다.
4. Security Group에서 검사를 진행한다. 승인을 받으면 해당 인스턴스로 들어가고 그렇지 못하면 Block 된다.
5. 트래픽이 작업을 완료하고 인스턴스에서 나가려할 때 다시 한번 Security Group을 만난다.
6. Security Group은 4번 과정에서 Stateful하게 검사를 진행했었기 때문에 검사를 진행하지 않고도 통과한다.
7. Security Group을 통과한 트래픽이 Subnet을 나가려고 할 때 다시 한번 NACL을 만난다.
8. NACL은 2번 과정에서 Stateless하게 검사를 진행했었기 때문에 다시 한번 검사를 진행한다.
9. NACL의 승인을 받은 트래픽은 최종적으로 VPC 외부로 나간다.


> ## AWS Route53

&nbsp; AWS에서 제공하는 DNS 서비스다. 즉, 내가 구축한 AWS Cloud Infra의 웹 애플리케이션에 대해 도메인을 등록할 수 있다.
<br>

### Client가 Route53 서비스를 이용하는 과정

![image](https://user-images.githubusercontent.com/49023663/126420076-8173d31e-d511-4199-bc6e-d8cb32f65574.png)

1. Client는 도메인을 웹 브라우저에 입력한다.
2. Client의 요청은 ISP가 관리하는 DNS 해석기(Resolver)로 라우팅 된다.
3. DNS 해석기는 DNS root name 서버로 요청을 보내서 .com TLD 서버의 위치를 얻어온다.
4. DNS 해석기는 얻어온 .com TLD 서버로 요청을 보내서 Route 53의 name 서버의 위치를 얻어온다. 이 때, DNS 해석기는 Route 53의 name 서버를 캐시한다. 그래서 다음에 또 어떤 Client가 이 도메인을 요청하면 3번과 4번은 건너뛴다.
5. DNS 해석기는 Route53 name 서버로 요청을 보낸다.
6. Route 53 name 서버는 해당 도메인의 IP주소를 알려준다.
7. DNS 해석기는 Client에게 요청을 IP주소로 응답한다.
8. 그럼 Client의 요청은 AWS 인스턴스에 해당하는 IP주소로 간다.
9. AWS 인스턴스는 Client에게 자원을 준다.