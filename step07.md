# Step 07. 클라이언트 테스트 및 정리(Cleanup)

## 목표
클라이언트 앱에서 기능을 검증하고 실습 종료 후 비용 방지를 위해 리소스를 정리합니다.

## 실습 순서

다양한 테스트를 진행하여 에이전트가 정상적으로 동작하는지 확인합니다. 앱이 실행된 터미널에서 아래의 프롬프트를 입력하고 결과를 확인합니다.

1. 정책 검색 테스트를 진행합니다. 이전 단계에서 업로드한 IT Policy 파일들의 그라운딩 데이터를 사용하여 응답을 하는 것을 확인합니다.

```text
What's the policy for password resets?
```

2. 성능 데이터 분석 테스트를 요청하여 Code Interpreter가 응답하는 것을 확인합니다.

```text
Analyze the system performance data and identify any periods where CPU usage exceeded 80%
```

3. 시각화 생성을 테스트합니다. 실행 된 애플리케이션은 차트와 인용 파일을 "agent_output" 폴더에 생성합니다. 차트가 정상적으로 생성되는지 확인합니다.

```text
Create a line chart showing memory usage trends over time
```

4. 통계 요약을 요청합니다.

```text
What are the average, minimum, and maximum values for disk usage in the performance data?
```

5. 상관관계 분석을 요청합니다.

```text
Find any correlation between high CPU usage and memory usage in the performance data
```

에이전트가 요청을 처리할 때 파일 검색(정책 관련 질문)과 코드 인터프리터(데이터 분석)를 함께 사용하는지 확인합니다. 코드 인터프리터는 CSV 데이터를 분석하고 계산을 수행하며 시각화도 생성할 수 있습니다.


6. 테스트가 끝나면 exit를 입력해 앱을 종료합니다.

## Cleanup
1. Foundry 포털에서 프로젝트로 이동합니다.
2. Settings > Delete project를 선택해 프로젝트를 삭제합니다.
3. 또는 Azure 포털에서 실습 리소스 그룹 전체를 삭제합니다.

## 실습 순서

* [개요. Build AI Agents with Portal and VS Code](README.md)
* [Step 01. Microsoft Foundry 프로젝트와 에이전트 생성](step01.md)
* [Step 02. 에이전트 지시문과 그라운딩 데이터 구성](step02.md)
* [Step 03. 포털에서 에이전트 테스트](step03.md)
* [Step 04. VS Code에서 에이전트 연결 및 테스트](step04.md)
* [Step 05. 에이전트 연동 클라이언트 애플리케이션 준비](step05.md)
* [Step 06. 환경 구성 후 애플리케이션 실행](step06.md)
* [Step 07. 클라이언트 테스트 및 정리(Cleanup)](step07.md)