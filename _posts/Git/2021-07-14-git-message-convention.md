---
title:  "[Git] Commit Message 작성 규칙"

categories:
  - Git
tags:
  - [Git Commit Message Convention]

toc: true
toc_sticky: true

date: 2021-07-14
last_modified_at: 2021-07-14
---



> ## 개요

&nbsp;Git을 이용하여 Version Control을 할 때는 여럿이서 작업을 한다. 그렇기에 Commit을 할 때의 메시지에 일관성을 유지하는 것이 좋다. 이 글에서는 많은 사람들이 사용하는 Commit Message Convention을 소개한다.

참고 자료<br>
<https://udacity.github.io/git-styleguide> <br>
<http://karma-runner.github.io/0.10/dev/git-commit-msg.html> <br>


> ## 메시지 구조

&nbsp;Commit 메시지는 다음과 같다.

```
type: Subject

body

footer
```

1. Type
&nbsp;Commit 메시지의 종류를 표기한다.
- feat: 새로운 기능이 추가된 경우
- fix: 버그를 수정한 경우
- docs: 문서를 수정한 경우
- style: 코드의 오류를 수정한 경우 (단, 코드의 수정은 없어야 한다)
- refactor: 코드를 리팩토링한 경우
- chore: 설정 파일을 변경한 경우 (단, 코드의 수정은 없어야 한다)

2. Subject
&nbsp;메시지의 내용을 간략히 작성한다. 
- 제한 조건
	- 현재 시제를 사용해야 한다.
	- 영문으로 작성 시 50자를 넘기지 않아야 한다.
	- 영문으로 작성 시 첫 문자는 대문자로 작성해야 한다.
	- 마침표를 사용하지 않는다.

3. Body
&nbsp;메시지의 내용을 상세하게 작성 해야 한다면 Body에 작성한다. 주로 무엇을(What), 왜(Why)에 해당하는 내용을 작성한다.
- 제한 조건
	- type: Subject와 Body 사이에 빈 줄을 하나 추가해야 한다.
	- 가독성을 위해 영문으로 작성시 한 줄은 최대 72자로 제한한다.

4. Footer
&nbsp;참조할만한 Issue가 있다면 추가해준다.

> ## 예시

```
feat: Add login in Backend
``` 

```
fix: Add redirect to Customer info

회원 정보 조회시 4xx 코드를 반환 하여 리디렉션을 추가함.

Resolves: #10
See also: #4, #32
```

```
chore: Add .gitignore
```
