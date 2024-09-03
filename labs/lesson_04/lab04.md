---
title: 'Lab 04: 모니터링'
layout: default
nav_order: 5
---
####  모니터링

#### 사전 요구 사항

AI 허브 리소스와 AI 검색 서비스를 생성할 수 있는 Azure 구독이 필요합니다.

#### 설정

이 랩을 수행하기 전에 1번 레슨 이후에 실행 중인 경우 이 단계에 대해 걱정할 필요가 없습니다.

그렇지 않은 경우, Azure AI Studio에서 프로젝트와 관련 리소스를 생성하고 GPT-4 모델을 배포하는 데 필요한 **설정**을 **1번 레슨**에서 따르세요.

#### 랩 단계

이 랩에서는 다음 단계를 수행합니다:

1) LLM 플로우 모니터링하기.

2) 솔루션에 콘텐츠 안전성 추가하기.

##### 1) LLM 플로우 모니터링하기

[배포된 애플리케이션의 품질과 안전성 모니터링](https://learn.microsoft.com/ko-kr/azure/ai-studio/how-to/monitor-quality-safety#user-experience) 문서의 **사용자 경험** 섹션에 설명된대로 모니터링에 필요한 메트릭을 계산하기 위해 워크플로우의 출력 노드를 수정하세요. 워크플로우를 배포할 때 모델 모니터링 섹션 내에서 "활성화" 버튼을 선택하여 모니터링을 활성화하세요.

##### 2) 솔루션에 콘텐츠 안전성 추가하기

기본 플로우를 처음부터 생성하세요 (간단한 농담 플로우를 구성할 것입니다).

아래 지침에 따라 플로우에 다음 조정을 수행하세요:

- 입력과 언어 모델 (LLM) 사이에 [콘텐츠 안전성 도구](https://learn.microsoft.com/ko-kr/azure/machine-learning/prompt-flow/tools-reference/content-safety-text-tool) 노드를 삽입하세요.

- 콘텐츠 안전성 도구의 출력을 처리하고 표준 플로우를 진행할지 여부를 결정하기 위해 Python 노드를 추가하세요. Prompt Flow에서 조건부 플로우를 생성하는 방법에 대한 안내는 [이 예제](https://github.com/microsoft/promptflow/tree/main/examples/flows/standard/conditional-flow-for-if-else)를 참조하세요.

- 기본 응답을 작성하기 위해 Python 노드를 추가하세요.

- 콘텐츠 안전성의 결과에 따라 다른 응답을 트리거할 조건을 구현하세요.

- 최종 노드를 개발하여 사용자에게 응답을 컴파일하고 전달하세요.