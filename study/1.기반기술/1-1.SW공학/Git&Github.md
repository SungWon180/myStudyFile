## 1-1. Git이란?

- 원하는 시점마다 깃발을 꽂고 깃발이 꽂힌 시점으로 자유롭게 이동 가능한 소스 코드 버전 관리 시스템.

## 1-2. Git의 기능

<details>
<summary>기능</summary>
<br>
- 버전관리 > 파일의 변경 이력을 기록하고, 불필요한 경우 이전 버전으로 되돌릴 수 있다.
<br>
<br>
- 브랜치 > 개발의 다양한 단계나 기능을 분리하여 관리할 수 있다.
<br>
이를 통해 여러 기능을 동시에 개발하고 통합하는 것이 용이하다.
<br>
<br>
- 협업 > 여러 개발자가 동일한 프로젝트에서 병렬로 작업할 수 있게 지원한다.
</details>

## 2-1. GitHub란?

- Git 호스팅 사이트 중 하나. 누구든지 기여할 수 있는 공개 저장소 프로젝트 > 오픈소스

## 2-2. GitHub의 기능

<details>
<summary>기능</summary>
<br>
- 저장소 호스팅 > 프로젝트의 소스 코드를 온라인에 저장하고 관리할 수 있다.
<br>
<br>
- Pull Request 및 Issue Tracking > 개발자들이 코드 변경 사항을 검토하고 통합하기 위한 매커니즘을 제공하며,
버그나 개선 사항을 추적할 수 있는 시스템을 제공한다.
<br>
<br>
- 문서화 및 위키 > 프로젝트 관련 문서와 정보를 저장하고 공유할 수 있는 위키 기능을 제공한다.
</details>

## 3-1. 전역 설정 값 입력

```bash

$ git config --global user.email "userEmail"
# 사용자의 이메일을 광역으로 설정하겠다.
$ git config --global user.name "userName"
# 사용자의 이름을 광역으로 설정하겠다.

$ git config --get user.email
# 사용자의 이메일 정보 조회
$ git config --get user.name
# 사용자의 이름 정보 조회

$ git config --global init.defaultBranch main
# 광역으로 초기 브랜치 값을 main으로 설정하겠다.

$ git config --get init.defaultBranch
# 초기 브랜치 정보 조회

$ git init
# 초기값 세팅

```
