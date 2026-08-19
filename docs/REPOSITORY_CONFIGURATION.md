## Repository 운영 구조

프로젝트는 역할에 따라 여러 Repository로 분리하여 관리합니다. 각 Repository는 독립적으로 코드와 Issue를 관리하지만, 동일한 개발 흐름과 Repository 설정을 사용합니다.

Repository는 Private을 기본으로 운영하며, 보안 및 민감 정보에 관련된 이슈가 없을 경우 Public으로 전환이 가능합니다.

Repository를 Public으로 전환하게 될 경우 Collaborators와 Branch Protection Rule을 점검합니다.

프로젝트 전체의 작업 현황은 각 Repository가 아닌 Organization의 **프로젝트 별 공용 Project**에서 통합하여 관리합니다.

! 작업을 시작하기 전에 다음 구조를 먼저 이해해주세요.

- 코드와 Issue는 각 프로젝트의 Repository에서 관리합니다.
- 각 프로젝트 하위 Repository의 전체 작업 현황은 `Organization Project`에서 관리합니다.
- 일반적인 개발은 `develop` 브랜치를 `source`로 진행합니다.
- 작업 브랜치에서 개발한 내용은 PR을 통해 `develop`에 반영합니다.
- 배포 가능한 상태가 되면 `develop`의 변경사항을 `main`에 반영합니다.
- Repository 접근 권한은 개인이 아닌 Organization Team을 기준으로 관리합니다.

## Branch

브랜치는 `main`과 `develop`을 중심으로 운영합니다.

```text
main
  └─ develop (default)
       ├─ feat/12/login
       ├─ feat/13/signup
       └─ fix/14/token
```

`main`은 실제 배포가 가능한 코드를 유지하는 **Production 브랜치**입니다.

`develop`은 평소 개발 내용을 통합하는 **Development 브랜치**입니다. 최초 Repository 생성 후 `main`을 Source로 생성하며, Repository의 Default Branch로 지정합니다.

따라서 새로운 기능을 개발하거나 버그를 수정할 때 `main`에서 직접 작업하지 않습니다. `develop`을 기준으로 새로운 작업 브랜치를 생성합니다.

예를 들어 Issue `#12`의 로그인 기능을 구현한다면 다음과 같이 작업할 수 있습니다.

```text
develop
  └─ feat/12/login
```

작업이 완료되면 해당 브랜치에서 `develop`을 Base로 Pull Request를 생성합니다.

```text
feat/12/login
      ↓ PR
   develop
```

여러 작업이 `develop`에 통합되고 배포 가능한 상태가 되면 `develop → main` Pull Request를 생성합니다.

결과적으로 개발한 코드가 이동하는 흐름은 다음과 같습니다.

```text
feat/* → develop → main
```

브랜치를 생성하는 방향과 변경사항을 병합하는 방향을 구분하면 다음과 같습니다.

```text
브랜치 생성
main → develop(default) → feat/*

변경사항 병합
feat/* → develop → main
```

## **Pull Request**

모든 개발 내용은 Pull Request를 통해 통합하는 것을 기본 흐름으로 사용합니다.

Merge 방식은 **Squash Merge**로 통일합니다. 작업 브랜치에서는 개발 과정에 따라 여러 Commit을 생성할 수 있지만, PR을 Merge할 때 하나의 Commit으로 합쳐 `develop` 또는 `main`에 반영합니다.

Squash된 Commit의 Message는 **PR Title**을 사용합니다. 따라서 PR Title은 해당 작업의 내용을 명확하게 표현하도록 작성합니다.

현재 기본으로 사용중인 Repository 들의 Pull Request 설정은 다음과 같습니다.

- Merge Commit: OFF
- Squash Merge: ON
- Rebase Merge: OFF
- Squash Commit Message: PR Title
- Update Branch 제안: ON
- Auto Merge: OFF
- Merge 후 작업 브랜치 자동 삭제: ON

작업 중 Base Branch인 `develop`에 새로운 변경사항이 추가되면 GitHub의 `Update branch` 기능을 이용해 최신 변경사항을 작업 브랜치에 반영할 수 있습니다.

PR이 Merge되면 사용이 끝난 작업 브랜치는 자동으로 삭제됩니다.

## **Branch Protection**

Private Repository를 기본으로 사용하고 있어, Branch Protection Rule은 적용하지 않습니다.

따라서 시스템적으로 직접 Push나 Merge를 제한하지는 않지만, `main`과 `develop`에 직접 작업하지 않고, 작업 브랜치를 생성하여 Pull Request를 통해 변경사항을 반영하는 흐름을 사용합니다.

## **Issue와 Project**

Issue와 Project는 서로 다른 범위에서 관리합니다.

Issue는 실제 코드가 존재하는 **각 Repository에서 생성하고 관리**합니다.

반면 Repository마다 별도의 Project를 만들지는 않습니다. 여러 Repository에서 발생하는 작업을 한곳에서 확인할 수 있도록 **Organization Project**를 사용합니다.

```text
App Repository ─────┐
                    │
Backend Repository ─┼─→ {ServiceName} Project
                    │
Infra Repository ───┘
```

따라서 개발자는 자신이 작업하는 Repository에서 Issue를 생성하고, 전체 프로젝트의 진행 상황이나 우선순위를 확인할 때는 Organization Project를 확인합니다.

이를 통해 Repository는 코드의 책임 영역에 따라 분리하면서도 프로젝트 전체의 작업 흐름은 하나의 공간에서 관리할 수 있습니다.

## **Access**

Repository 접근 권한은 구성원 개인에게 하나씩 부여하는 방식보다 **Organization Team 단위로 관리**합니다.

각 Team에는 담당하는 Repository에 `Write` 권한을 부여합니다.

```text
Organization
└─ Development Team
   ├─ App Repository      → Write
   ├─ Backend Repository  → Write
   └─ Infra Repository    → Write
```

새로운 개발자가 프로젝트에 참여하면 각 Repository에 개별적으로 권한을 추가하기보다 해당 Team에 구성원을 추가합니다.

이를 통해 Repository가 늘어나거나 구성원이 변경되더라도 Team을 기준으로 일관된 접근 권한을 유지할 수 있습니다.

## **개발 흐름**

새로운 작업을 시작했다면 기본적으로 다음 순서로 진행합니다.

```text
1. 담당 Repository에서 Issue 확인 또는 생성
        ↓
2. develop을 기준으로 작업 브랜치 생성
        ↓
3. 기능 개발 및 Commit
        ↓
4. 작업 브랜치 → develop PR 생성
        ↓
5. Squash Merge
        ↓
6. 작업 브랜치 자동 삭제
        ↓
7. 배포 가능한 상태가 되면 develop → main PR
```

즉, 일반적인 개발에서는 `develop`을 중심으로 작업하고 `main`은 배포 가능한 상태를 유지하는 것을 기본 원칙으로 합니다.


## Repository 설정
새로운 Repository를 생성할 경우 기본적으로 아래의 설정을 따릅니다.

#### General
- **Default branch**
	- `develop`
- **Features**
	- Issues: `ON`
	- Discussions: `OFF`
	- Projects: `OFF`
	- Pull requests: `ON`
		- Pull request permissions →` Collaborators only`
- **Pull Requests**
	- Merge Commit: `OFF`
	- Squash Merge: `ON`
		- `Squash Commit Message: PR Title`
	- Rebase Merge: `OFF`
	- Always suggest updating pull request branches: `ON`
	- Auto Merge: `OFF`
	- Automatically delete head branches: `ON`
#### Commits
- Allow comments on individual commits: `ON`
#### Issues
- Auto-close issues with merged linked pull requests: `OFF`
