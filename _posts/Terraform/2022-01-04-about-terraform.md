---
title:  "[Terraform] 테라폼이란?"

categories:
  - Terraform
tags:
  - [terraform, IaC]

toc: true
toc_sticky: true

date: 2022-01-04
last_modified_at: 2022-01-04
---

> ## 개요

&nbsp; Terraform은 Hasicorp에서 개발한 IaC 툴이다. 즉, Infra를 Code로 이용하여 안전하고 효율적으로 구축할 수 있다. 스토리지, 네트워킹과 같이 IaaS 수준부터 SaaS와 같이 고차원 수준까지 지원한다. 이 글에서는 Cloud Infra를 설계하고 구축할 때 Terraform을 어떻게 사용하는지에 대해 설명한다.

> ## Terraform을 사용하기 위해 알아야할 것

### IaC

&nbsp; Terraform에서 HCL을 사용하기 위한 에디터는 따로 정하지 않았다. 자신이 편한 에디터를 사용하면 좋지만, Visual Code에 Hashicorp에서 Terraform을 위한 Extenstion을 만들어 놓았으니 Visual Code를 추천한다.

### Provider

&nbsp; Provider란, Terraform이 CSP, SaaS, API와 같은 것들과 상호작용하기 위해 사용하는 Plugin이다. 즉, Cloud를 Terraform으로 구축한다면, 자신이 선택한 CSP의 Provider를 설치해야 한다. <br>
&nbsp; Provider마다 사용법이 같지 않기 때문에 공식 Document를 참고하는 것을 추천한다. <br>
[참고 사이트](https://registry.terraform.io/search/providers?namespace=hashicorp)

![image](https://user-images.githubusercontent.com/49023663/148066691-e92e89d9-8381-4116-aaeb-7d91ba38e613.png)

> ## Terraform 사용법

![image](https://user-images.githubusercontent.com/49023663/148063057-a3ca6fb2-cb66-45e9-809b-afa5de798d06.png)

&nbsp;IaC를 위한 코딩이 완료 되었다면 오류 확인 및 Provider에 적용이 된다. 오류 확인 과정을 Terraform에서는 Plan이라 부르고, 적용은 Apply라고 한다. Plan과 Apply 과정은 Hashicorp에서 제공하는 Terraform CLI를 설치하고 Configuration을 진행한 후에 가능하다.<br>
&nbsp;처음 Terraform을 시작한다면 Configuration 과정에서 많은 오류가 있을 것이다. 환경변수 설정, CSP 키 설정, Terraform 프로젝트 초기화와 같은 작업들을 빠짐없이 완료해야 Plan부터 시작이 가능하다.

> ## Terraform Cloud

&nbsp; 협업 시 VCS가 필요할 때 사용할 수 있는 서비스다. Hashicorp에서 제공하기 때문에 Terraform과의 호환성이 매우 뛰어나다. Terraform Cloud는 Workspace로 프로젝트를 분류하기 때문에 여러개를 동시에 진행할 수 있다.<br>
&nbsp; Workspace를 만들 때, 3가지 방법이 존재한다. CLI 기반으로 로컬에서 진행하거나, Github과 같은 VCS를 사용하거나, Terraform API를 사용할 수 있다.