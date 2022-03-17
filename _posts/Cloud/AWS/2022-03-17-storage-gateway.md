---
title:  "[AWS] Storage Gateway 개념 및 종류"

categories:
  - AWS
tags:
  - [Storage Gateway, File Gateway, Volume Gateway, Tape Gateway]

toc: true
toc_sticky: true

date: 2022-03-17
last_modified_at: 2022-03-17
---

> ## 개요

&nbsp;이 글에서는 Amazon에서 제공하는 서비스인 AWS Storage Gateway에 대해 알아본다.

> ## 개념

&nbsp; Storage Gateway는 On-prem 환경의 스토리지와 AWS Cloud 환경의 스토리지를 연결할 때 사용하는 게이트웨이다. 즉 고객이 On-prem과 AWS Cloud 환경 모두에 스토리지를 사용하고 싶을 때, On-prem 측에 VM 방식으로 Storage Gateway를 배치할 수 있다. 구성도는 아래와 같다.

![image](https://user-images.githubusercontent.com/49023663/158736491-c382f511-03ee-4e50-8d33-212fee93af70.png)

&nbsp; 그림에서 확인할 수 있듯이, On-prem 옆에 붙을 수 있는 Storage Gateway에는 3가지 종류가 있다. 각각 사용하는 프로토콜이나 특징이 다르기 때문에 사용 용도도 다르다.

> ## 종류

### 1. File Gateway

&nbsp; File Gateway는 NFS (Network File System)나 SMB (Server Message Block)와 같은 표준 파일 프로토콜을 사용한다. 이 때, AWS Cloud 환경의 스토리지가 무엇인지에 따라 2가지로 나뉜다.

#### S3 File Gateway

&nbsp; AWS Cloud 환경의 스토리지가 S3일 때 사용하는 게이트웨이다. 즉, On-prem의 데이터를 객체로 변환하여 S3에 저장하고 검색할 수 있다.

![image](https://user-images.githubusercontent.com/49023663/158742435-4d7cac42-e74c-4e20-8664-10731332f2cb.png)


####  FSx File Gateway

&nbsp; AWS Cloud 환경의 스토리지가 FSx일 때 사용하는 게이트웨이다. FSx는 윈도우 계열 파일 서버이기 때문에, 윈도우 파일을 공유해야할 때 사용할 수 있다.

![image](https://user-images.githubusercontent.com/49023663/158742141-59b7cc1d-ce38-4801-a80c-34a8d098b327.png)

---

### 2. Volume Gateway

&nbsp; Volume Gateway는 iSCSI (Internet Small Computer System Interface) 프로토콜을 사용한다. 이 때, Volume Gateway의 사용 용도에 따라 2가지로 나뉜다.

#### Stored Volume Gateway

&nbsp; Volume Gateway를 주 저장소로 사용할 때 사용하는 게이트웨이다. 그리고, AWS Cloud 환경의 스토리지는 부 저장소로 사용하며, S3 뿐만 아니라 AWS Backup이나 AWS EBS를 사용할 수 있다. 즉, AWS Cloud 환경의 스토리지는 백업용이며, 비동기 방식으로 증분 백업된다.

![image](https://user-images.githubusercontent.com/49023663/158740639-0abb2498-6914-4c62-a1e5-c7923e5e4269.png)

#### Cached Volume Gateway

&nbsp; Volume Gateway를 캐시 스토리지로 활용하고 싶을 때 사용하는 게이트웨이다. 그리고, AWS Cloud 환경의 스토리지가 주 저장소다. 즉, On-prem이 자주 검색하는 정보만 Volume Gateway에 저장한다.

![image](https://user-images.githubusercontent.com/49023663/158740677-1ce4cbaf-1679-493d-95d2-58461a1477b9.png)

---

### 3. Tape Gateway

&nbsp; Tape Gateway는 VTL (Virtual Tape Library) 프로토콜을 사용한다. AWS Cloud 환경에 데이터를 장기적으로 보관해야할 때 주로 사용한다. 그래서 주로 S3 중에서 Glacier류가 쓰인다.

![image](https://user-images.githubusercontent.com/49023663/158741299-46c12567-4554-4998-a686-6274404d6205.png)