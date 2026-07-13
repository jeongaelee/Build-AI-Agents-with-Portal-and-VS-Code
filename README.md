# Build AI Agents with Portal and VS Code (한글 가이드)

원문: [Build AI agents with portal and VS Code | Develop AI Agents in Azure](https://microsoftlearning.github.io/mslearn-ai-agents/Instructions/Exercises/01-build-agent-portal-and-vscode.html)

## Microsoft Foundry 소개

<img src="https://ai.azure.com/favicon.ico" alt="Microsoft Foundry logo" width="64"/>

Microsoft Foundry는 엔터프라이즈 환경에서 AI 에이전트를 설계, 개발, 테스트, 배포까지 통합적으로 진행할 수 있도록 지원하는 개발 플랫폼입니다.

- 프로젝트 단위로 모델, 데이터, 도구, 에이전트를 체계적으로 관리할 수 있습니다.
- 포털과 VS Code(Foundry Toolkit)를 연계해 로우코드와 코드 기반 개발을 함께 활용할 수 있습니다.
- 파일 검색, 코드 인터프리터, 평가/관측 기능을 통해 실무형 에이전트를 빠르게 반복 개선할 수 있습니다.

## Hands-on Lab 개요

이 실습에서는 Microsoft Foundry 포털과 Foundry Toolkit for VS Code 확장을 함께 사용하여 AI 에이전트 솔루션을 구성합니다.
먼저 포털에서 그라운딩 데이터와 기본 도구를 포함한 에이전트를 만들고, 이후 VS Code에서 프로그래밍 방식으로 에이전트와 상호작용하며 코드 인터프리터 같은 고급 기능을 활용합니다.

예상 소요 시간: 약 45분

> 참고
> 이 실습에서 사용하는 일부 기술은 미리 보기(Preview) 또는 활발히 개발 중인 기능일 수 있습니다. 환경에 따라 경고나 예상치 못한 동작이 발생할 수 있습니다.

## Prerequisites (사전 요구 사항)

실습을 시작하기 전에 아래 항목을 준비하세요.

- 충분한 권한 및 할당량(Quota)이 있는 [Azure 구독](https://azure.microsoft.com/free/)
- 로컬 PC에 설치된 [Visual Studio Code](https://code.visualstudio.com/)
- [Python 3.13](https://www.python.org/downloads/) 이상
- 로컬 PC에 설치된 [Git](https://git-scm.com/downloads)
- Azure AI 서비스 및 Python 프로그래밍에 대한 기본 이해

> 참고
> Python 3.13은 사용 가능하지만, 일부 종속성은 해당 버전에 대해 아직 컴파일되지 않았을 수 있습니다. 본 랩은 Python 3.13.12 환경에서 정상 동작이 확인되었습니다.

## 다음 단계

* [Step 01. Microsoft Foundry 프로젝트와 에이전트 생성](step01.md)

## 실습 순서

* [개요. Build AI Agents with Portal and VS Code](README.md)
* [Step 01. Microsoft Foundry 프로젝트와 에이전트 생성](step01.md)
* [Step 02. 에이전트 지시문과 그라운딩 데이터 구성](step02.md)
* [Step 03. 포털에서 에이전트 테스트](step03.md)
* [Step 04. VS Code에서 에이전트 연결 및 테스트](step04.md)
* [Step 05. 에이전트 연동 클라이언트 애플리케이션 준비](step05.md)
* [Step 06. 환경 구성 후 애플리케이션 실행](step06.md)
* [Step 07. 클라이언트 테스트 및 정리(Cleanup)](step07.md)
