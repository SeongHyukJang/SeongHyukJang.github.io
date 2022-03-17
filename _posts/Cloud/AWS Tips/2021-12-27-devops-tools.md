---
title: "[AWS] AWS에서 DevOps 환경 구축하기"

categories:
  - AWS Tips
tags:
  - [Code Series, CodePipeline, CodeCommit, CodeBuild, CodeDeploy, DevOps]

toc: true
toc_sticky: true

date: 2021-12-27
last_modifed_at: 2021-12-27
---



> ## 개요

&nbsp; AWS에서는 고객이 DevOps 방식을 사용할 수 있는 서비스들을 제공한다. 애플리케이션의 개발, 통합, 배포와 같은 작업들을 파이프라인을 구성하여 자동화가 가능하고 다양한 써드 파티 소프트웨어와 함께 사용이 가능하다.

> ## Code Series

&nbsp; Code Series는 AWS에서 DevOps 방식을 사용하기 위해 존재하는 서비스들의 집합이다. 대표적으로 AWS CodeCommit, CodeBuild, CodeDeploy, CodePipeline이 있다.

### AWS CodeCommit
<br>
&nbsp; AWS에서 제공하는 Git 기반 코드 관리 서비스다.

- Git 기반이기 때문에 버전이나 소스 코드를 관리하기 간편하다.
- AWS에서 제공하는 서비스들뿐만 아니라, 써드 파티 소프트웨어나 다양한 IDE와 연동이 가능하다.
- SSH로 접근하고 싶다면 AWS CLI를 설치하고 Configuration을 진행해야 가능하다.

&nbsp; AWS CodeCommit을 사용하는 방법은 아래와 같다.

![image](https://user-images.githubusercontent.com/49023663/147471371-792e1db0-5561-47ea-a766-be2b16ad5dd3.png)

1. AWS CLI 또는 AWS 콘솔에 로그인 한다.
2. AWS CodeCommit 서비스를 검색하여 Repository를 생성한다.
3. 생성된 Git 기반 Repository를 SSH 또는 HTTPS 방식으로 복제한다.
4. 코드를 추가하고 Git 기반으로 Push 한다.

<br>

### AWS CodeBuild
<br>
&nbsp; 빌드(Build)를 도와주는 AWS 서비스다. 컴파일과 테스트를 자동으로 진행해주며 소프트웨어 패키지를 생성한다.

- YAML 파일로 빌드하는 컴퓨터의 환경을 설정할 수 있다.
- 빌드 도중에 원하는 명령어를 사전에 추가하여 실행할 수 있다.
- 빌드에 필요한 소스 코드를 AWS CodeCommit, S3, Github, Bitbucket과 같이 다양한 곳과 연동이 가능하다.
- Jenkins와 같은 써드 파티 소프트웨어와 연동이 가능하다.

&nbsp; AWS CodeBuild를 사용하는 방법은 아래와 같다.

![image](https://user-images.githubusercontent.com/49023663/147471603-5c9e8d57-892b-4855-bd17-831d5d5babf5.png)


1. AWS CodeBuild를 사용하기 위해 필요한 YAML 파일인 buildspec.yaml을 추가하고 Repository에 Push한다.
2. AWS CLI나 AWS 콘솔을 통해 확인한다.
3. AWS CodeBuild에서 빌드 환경을 설정하고 빌드를 진행한다.
4. 빌드 성공 여부를 확인한다. 만약 실패 했다면 로그를 통해 원인을 찾아서 해결한다.

<br>

### AWS CodeDeploy
<br>
&nbsp; 배포(Deploy)를 도와주는 AWS 서비스다.

- Server, Serverless, Container등 여려 종류의 애플리케이션을 배포할 수 있다.
- On-prem 뿐만 아니라 AWS Lambda, ECS, EC2에도 배포가 가능하다.
- 서비스가 중단되지 않고 여러 애플리케이션에 배포가 가능하다. In-Place와 Blue/Green 방식을 지원한다.

&nbsp; AWS CodeDeploy를 사용하는 방법은 아래와 같다.

![image](https://user-images.githubusercontent.com/49023663/147472415-9197f04f-e850-45a3-b800-2d16b34dd179.png)

1. AWS CodeDeploy를 사용하기 위해 필요한 YAML 파일인 appspec.yaml을 추가하고 Repository에 Push한다.
2. AWS CLI나 AWS 콘솔을 통해 확인한다.
3. AWS CodeBuild에서 빌드 환경을 설정하고 빌드를 진행한다.
4. 빌드 성공 여부를 확인한다. 만약 실패 했다면 로그를 통해 원인을 찾아서 해결한다.
5. AWS CodeDeploy에서 배포를 진행할 그룹을 설정한다. 그림에서는 AWS EC2가 배포를 진행할 그룹이 된다.
6. 배포 진행 전, appspec.yaml에 문제가 없는지 확인한다. 문제가 있을 시, 1번부터 다시 진행한다.
7. 배포에 필요한 설정이 완료되면 배포 그룹에 배포를 진행한다.
8. 배포 성공 여부를 확인한다. 만약 실패 했다면 로그를 통해 원인을 찾아서 해결한다.


<br>

### AWS CodePipeline
<br>
&nbsp; CI/CD 파이프라인의 자동화를 가능하게 해주는 AWS 서비스이다. 즉, 위에서 설명한 서비스들을 하나의 파이프라인으로 구성이 가능하다.
<br>

![image](https://user-images.githubusercontent.com/49023663/147469664-4f2b887d-641b-42e6-8855-c1a2315cb259.png)

<br>
- AWS 콘솔을 통해 CI/CD 파이프라인의 상태를 실시간으로 점검할 수 있다.
- AWS Code Series 뿐만 아니라 써드 파티 소프트웨어를 연동할 수 있다.

<br>

> ## CI/CD 파이프라인 자동화

&nbsp; AWS CodeSeries를 사용하여 CI/CD 파이프라인을 자동화한 예시다.

![image](https://user-images.githubusercontent.com/49023663/147472780-06083bce-d7a7-4617-acf1-e7dec045391e.png)

1. AWS CodeCommit에 필요한 소스 코드를 Push한다.
2. AWS CodeBuild를 사용하여 Build 한다.
3. 배포하기 전에 AWS SNS를 통해 관리자의 승인을 받는다.
4. AWS CodeDeploy를 통해 배포 그룹인 EC2에 자동으로 배포된다.