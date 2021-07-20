---
title:  "[AWS] Global Infrastructure"

categories:
  - AWS
tags:
  - [Region, Availability Zone, Multi AZs, Edge Location, AWS CloudFront]

toc: true
toc_sticky: true

date: 2021-07-18
last_modified_at: 2021-07-18
---



> ## 개요

&nbsp;AWS는 다양한 국가에 IDC를 설치하여 Cloud 서비스를 제공한다. 전세계에 존재하는 고객들에게 고가용성(이하 HA라 함) 및 Fault Tolerance를 제공하기 위해서다. 여기서 Fault Tolerance는 데이터센터에 발생할 수 있는 자연재해나 정전과 같은 것들이 포함된다.<br>
&nbsp;AWS가 제공하는 서비스를 제대로 활용하기 위해서는 이러한 AWS의 Global Infrastructure를 이해하고 있어야 한다.

> ## Region

### 개념

&nbsp;AWS가 IDC를 설치한 국가를 Region이라 한다. 한국은 'ap-northeast-2'라는 이름으로 서비스 되고 있다. 보통 AWS를 이용하여 Cloud 인프라를 설계할 때 한 리전에 설계한다. 이 때, 리전을 선택하는 요인들이 존재한다.

### Region 선택할 때 고려해야할 요소
1. Compliance
- 몇 개의 국가에는 Infra를 설계할 때 통제나 법규가 있을 수 있다. 그래서 제일 우선적으로 고려해야 할 사항이다.
2. Proximity
- Low Latency를 위해 주요 고객들이 위치한 곳과 물리적으로 가까운 리전을 선택하는 것이 좋다.
3. Feature Availability
- 몇몇 리전에는 AWS에서 아직 제공하지 않는 서비스가 존재한다. 만약 사용해야 하는 서비스가 해당 리전에서 제공하지 않는다면, 다른 리전을 선택해야 한다.
4. Pricing
- 리전들마다 AWS 서비스의 가격이 다르다. 그래서 만약 서비스를 여러 개의 리전을 고려한다면, 예산에 맞는 리전을 선택해야 한다.

> ## Availability Zone (AZ)

### 개념

&nbsp;AWS는 특정 리전에 IDC를 설치할 때 여러 개를 설치한다. 이 역시 리전과 마찬가지로 HA와 Fault Tolerance를 보장하기 위해서다. 예를 들어, 특정 국가에 한 곳에만 IDC를 설치했는데 자연재해가 발생하여 서비스에 장애가 발생하는 상황 같은 것을 방지하기 위해서다.<br>
&nbsp;리전에서 IDC들이 위치한 곳을 Availability Zone 즉, 가용 영역이라고 부른다. 한국에는 현재 총 4개의 Availability Zone을 제공한다.

### 설계 방법

&nbsp;한 리전에서 AWS Cloud Infra를 설계할 때, 설계자는 Availability Zone을 선택해야 한다. 이 때, HA와 Fault Tolerance를 위해 2개 이상의 Availability Zone을 사용하는 것이 적절하다. 이러한 설계 방법을 Multi AZs라고 한다.<br>
&nbsp; AWS에서 HA와 Fault Tolerance를 위해 제공하는 서비스들은 당연하게도 Multi AZs를 지원한다. 예시로 Auto Scaling Group과 Elastic Load Balancing 서비스가 있다.

> ## Edge Location

### 개념

&nbsp;AWS는 Region과 Availability Zone과 별개로 Edge Location에도 서버를 설치했다. 용도는 IDC에 존재하는 데이터들을 캐시(cache)하여 짧은 지연시간을 위함이다. 즉, 캐시 서버이기 때문에 기존의 IDC에 비해 사이즈가 작다.<br>
&nbsp;우리 서비스의 주요 고객층이 한국인이여서 한국 리전에 Cloud Infra를 설계하고 구축 했다고 가정하자. 만약 해외에 있는 사람이 이 서비스를 이용하고 싶고 Edge Location에 캐시 서버가 없다면 매번 물리적으로 먼 거리를 통해 데이터를 가져올 것이다. 이러한 상황을 방지하기 위해 Edge Location이 사용된다.<br><br>
&#8251;&nbsp;[리전별 캐시되는 Edge Location](https://aws.amazon.com/ko/cloudfront/features/?whats-new-cloudfront.sort-by=item.additionalFields.postDateTime&whats-new-cloudfront.sort-order=desc)

### AWS CloudFront

&nbsp;AWS에서 제공하는 Content Delivery Network(CDN) 서비스다. 이 때, Edge Location에 존재하는 캐시 서버를 이용하여 서비스를 제공한다. 주로 Amazon S3와 함께 사용하는데 과정은 다음과 같다. <br><br>
![image](https://user-images.githubusercontent.com/49023663/126170684-b301cc38-c27a-4015-9a1c-1a6bad4a22bc.png)

1. Amazon S3 버킷을 생성한다. 이 때, S3는 퍼블릭 엑세스여야 한다. 그리고 S3를 CloudFront를 이용하여 세계 곳곳에 존재하는 Edge Location에 배포한다.
2. S3 버킷에 콘텐츠를 업로드한다.
3. 고객이 업로드된 콘텐츠를 요청할 경우, 곳곳에 존재하는 Edge Location 중에서 어느 곳에서 콘텐츠를 가져올지 결정한다. 이를 Web Distribution이라 한다.
4. 결정된 곳의 도메인 네임을 고객에게 응답으로 준다.
5. 고객의 요청을 처리한 후에 추가 작업으로 CloudFront는 3번에서 만든 Distribution 구성을 POP(Point of Presence)로 보낸다.