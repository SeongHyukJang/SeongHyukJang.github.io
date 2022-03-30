---
title:  "[Terraform] 테라폼의 기본 구성 파일과 모듈"

categories:
  - Terraform
tags:
  - [terraform, IaC]

toc: true
toc_sticky: true

date: 2022-03-07
last_modified_at: 2022-03-07
---

> ## 개요

&nbsp; 이 글에서는 Terraform을 활용하여 Cloud Infra를 구축할 때 어떻게 파일과 디렉토리를 설계해야 하는지 설명한다. 무조건 이 방식으로 설계해야 하는 것은 아니지만 Terraform에서 권고하는 방법이며, 효율적으로 코드를 관리할 수 있다는 장점이 있다.

> ## 기본 구조

&nbsp; Terraform의 기본 구조는 아래와 같다.

<img width="425" alt="image" src="https://user-images.githubusercontent.com/49023663/159108617-116eda8d-ff75-4bbe-a372-349f7ca6a2f8.png">

### main.tf

<code>main.tf</code>는 테라폼의 엔트리 포인트다. 즉, python의 main.py나 c++의 main.cpp와 같은 역할을 한다. 해당 파일에 주로 작성하는 정보는 다음과 같다.

- Provider의 정보 및 버전이나 설계자의 이름과 같은 사전 구성 정보를 작성한다.
- 사용하는 모듈을 명시하고 세부정보를 작성한다.
- 모듈을 사용하지 않고 특정 리소스에 대한 정보를 작성할 수도 있다.

### variables.tf

<code>variable.tf</code>는 파일명에서 알 수 있듯이, 변수에 대한 정보를 작성하는 파일이다. 변수를 사용하지 않는 방식인 하드 코딩 방식으로 Infra를 설계할 수 있지만 관리의 효율성을 위해 모든 구성 정보를 변수로써 지정하는 것이 좋은 습관이다. 문법은 다음과 같다.

```
variable "변수 이름" {
  type = "변수 타입"
  default = "변수의 디폴트 값"
  description = "변수의 세부설명"
}
```

※ [Terraform 변수 타입](https://www.terraform.io/language/expressions/types)

### outputs.tf

<code>outputs.tf</code>는 Terraform으로 Infra를 구축한 후에 설정한 결과를 확인하기 위한 정보를 작성하는 파일이다. 예를 들어, EC2를 생성한 후에 EC2의 식별자, Private IP, Public IP와 같은 정보를 알 수 있다.

```
output "변수 이름"{
  value = "확인할 리소스 정보"
}
```

만약 자식 모듈에서 output 파일이 있고 부모 모듈에서 접근하는 방법은, 부모 모듈의 outputs.tf에서 다음과 같이 작성한다.

```
output "변수 이름"{
  value = module."자식 모듈 이름"."자식 모듈에서의 변수 이름"
}

```

> ## 모듈을 이용한 디렉토리 구성

Terraform을 이용하여 아키텍처를 구축할 때, main.tf에 모든 리소스 정보를 넣을 수도 있고, 서비스별로 구분해서 여러 .tf파일을 만들 수도 있다. 하지만, 추후에 관리를 용이하게 하기 위해서는 모듈을 사용하여 구성하는 것이 좋다. 예시는 아래와 같다.

<img width="161" alt="image" src="https://user-images.githubusercontent.com/49023663/160776447-1b43b83e-6abf-4ca7-ab85-d88405e9655f.png">