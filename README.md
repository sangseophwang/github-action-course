# github-action-course

## Github Action
### 정의
- Github에서 제공하는 CI/CD DevOps 파이프라인 자동화 플랫폼
- 소프트웨어 개발 및 배포 과정 자동화를 목적으로 제공되는 서비스

### Github Action 활용 케이스
- CI/CD
    - Continuous Integration
    - Continuous Deployment
    - 코드 변경 사항이 push될 때, 코드 검토 - 테스트 - 빌드 단계를 자동으로 수행
    - 빌드된 결과를 자동으로 배포할 수 있도록 트리거 역할
- 테스트 자동화
    - 코드 변경에 대해 자동으로 단위 테스트(Unit test), 통합 테스트(Integration test), E2E 테스트 등을 실행
    - 변경 사항에 대한 검증 자동화를 통해 코드 품질 유지
- 코드 협업 관련 자동화
    - PR 시 자동으로 리뷰어 지정
    - 코드 스타일 가이드 준수 여부 확인을 통해 협업 과정 개선
- 보안 검사
    - 코드 보안 취약점 스캔
    - 라이브러리 종속성 검사 등을 자동 수행
- 컨테이너 이미지 빌드
    - 코드 변경 시 컨테이너 이미지를 자동으로 빌드 후, Docker Hub이나 AWS ECR 등에 자동으로 푸시하는 이미지 빌드 자동화 수행
- 문서 자동화
    - 코드 기반 API 문서 가이드를 자동으로 반영하여 문서 최신화 수행
- 프로젝트 이슈 관리 자동화
    - 이슈 및 버그 리포트 시 템플릿 자동 설정
    - 이슈 라벨링 자동화를 통해 프로젝트 관리 운영 자동화 적용


## Workflow
### 정의
- 이벤트 발생 시 어떤 행위를 할 것인가에 대한 작업 정의
- 자동화 작업의 가장 상위 개념으로, 하나 이상의 Job을 실행시키는 자동화 구성
- Github Repository 내 `.github/workflows` 디렉토리에 YAML 파일 단위로 정의
- Repository에서 발생되는 이벤트(커밋 추가, 이슈 생성 등)가 발생할 때 자동 실행

### Workflow 세부 내역
- name
  - 워크플로우의 이름. UI 상 워크플로우 이름 표시
- on
  - 워크플로우 실행을 위한 트리거 정의
  - workflow_dispatch는 사용자에 의한 수동 트리거
- 작업(Job)
    - 워크플로우 내 작동하는 작업 단위
- 작업기(Runner)
    - 작업별 별도의 공간에서 실행. 작업 공간에 대한 정의
- 스텝(Step)
    - 작업 내 개별 실제 수행되는 액션에 대한 정의

### Workflow Trigger
- 워크플로우를 실행하게 하는 이벤트
- 트리거를 어떻게 설정하느냐에 따라 워크플로 실행 조건을 다르게 구성
- 종류
  - 워크플로가 위치한 Repository에서 발생한 이벤트
  - Repository 외 관련 서비스에서 발생한 이벤트
  - 예약된 시간에 트리거 발생
  - 수동으로 직접 이벤트 트리거 발생

## Variables
### 정의
- Workflow 파일에서 사용되는 동적인 값
- 보다 유연한 워크플로 사용을 가능케 하며, 주로 환경 변수, 시크릿 값을 저장하고 접근할 때 사용
- Job 또는 Workflow 단계에서 실행되는 명령은 변수를 만들고 읽고 수정할 수 있음

### 특징
- 개별 변수값의 최대 데이터 사이즈는 48kb
- 최대 1,000개 조직 변수, 500개 레포지토리 변수
- 변수 우선 순위
  - 범위가 작은 것이 우선
  - 조직 변수 < 워크플로 변수
- 변수명 규칙
  - 이름에 영문, 숫자 또는 언더바만 사용 가능
  - 공백 허용 X
  - `GITHUB_` 로 시작하지 않을 것
  - 숫자로 시작 불가
  - 대소문자 구분 X

### Variables 생성 방식
- Workflow 파일에 env 키로 정의하여, 단일 Workflow에서 정의하는 방식
  - env 키 위치에 따라 변수 적용 범위(scope)가 달라짐
- Github Organization 또는 Repository에서 정의하는 방식
  - Settings → Security → Secrets and variables → Actions
  - 변수 접근 키로 `vars` 사용

### Default Variables
- Github에서 내부적으로 관리 중인 기본 환경 변수
- Prefix로 GITHUB_* 또는 RUNNER_* 로 설정된 네이밍 사용

### Default Variables 예시
- GITHUB_ACTION: 현재 실행중인 Action ID
- GITHUB_ACTION_PATH: 현재 실행중인 Action 경로
- GITHUB_ENV: 러너 내 변수 설정 파일 경로
- GITHUB_EVENT_NAME: 워크플로우를 트리거한 이벤트 이름
- GITHUB_WORKFLOW: 워크플로우 이름
- RUNNER_ARCH: Runner의 architecture(X86, ARM, ARM64..)
- RUNNER_OS: Runner의 운영체제(Linux, Windows, macOS)

### 작업(Job) 데이터 활용
- Step 간 데이터 전달
  - 워크플로의 경우 작업 단위로 수행
  - 작업 내 여러 개의 Step이 있으며, 각 Step은 정의된 고유의 작업을 실행하는 형태
  - `$GITHUB_OUTPUT` 을 활용해 전달 가능
  - `key=value` 형태로 `$GITHUB_OUTPUT` 에 기록
    - `echo "random_id=$RANDOM" >> "$GITHUB_OUTPUT"`
  - `steps.<step_id>.outputs.<key>` 로 데이터 접근
- Job을 넘어서 다른 Job에 데이터 전달
  - Runner가 다르기 때문에 기본적으로는 전달 불가
  - - Artifact와 같은 다른 기능을 사용하거나, 외부 스토리지를 활용하는 형태로 가능

## 조건 및 연산자
- Expression
  - 특정 구문을 프로그래밍 형태로 설정하여 결과 활용
  - `${{ expression }}`
- Condition & Operator
  - 조건식 if 키워드를 활용
  - `if: github.repository == 'xxxxx/github-action-course'`
- Functions
  - 조건식 if와 함께 다양한 기본 함수 사용 가능
  - 예시
    - contains
    - startsWith
    - endsWith
    - format: 특정 포맷의 string 값을 변수로 변경
    - join: Array 배열의 문자열 값을 연결
- Status check functions
  - success()
  - failures()
  - canceled()
  - always()
