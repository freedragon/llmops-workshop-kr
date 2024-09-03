---
title: '성능 평가'
layout: default
nav_order: 1
parent: 'Lab 06: 성능 평가'
---
# 성능 평가

언어 모델 (LLM) 애플리케이션을 개발할 때는 개발과 평가에 상당한 시간을 투자합니다. 이는 애플리케이션이 신뢰할 수 있는 소스에 기반한 고품질의 응답을 생성하고 사용자에게 해를 끼치지 않는 것을 보장하기 위한 것입니다.

그러나 LLM 애플리케이션의 사용자 경험의 효과성은 응답의 품질뿐만 아니라 사용자가 이러한 응답을 얼마나 빨리 받는지에 따라 결정됩니다. 따라서 우리의 논의는 주로 빠른 응답 시간을 위해 설계된 LLM 애플리케이션의 평가에 중점을 둡니다.

<!-- **성능 엔지니어링**은 성능 튜닝과 같은 실천을 통해 애플리케이션 성능을 최적화하고, 생산 중에 문제와 병목 현상을 식별하는 성능 모니터링과 같은 실천을 통해 애플리케이션 기능을 향상시킵니다. 이러한 문제가 최종 사용자에게 영향을 미치기 전에 이러한 문제를 예측하고 해결할 수 있는 능력은 매우 중요합니다. 이것이 **성능 평가**의 역할이 중요해지는 곳입니다. 이 텍스트의 주요 주제입니다. -->

**성능 평가**의 목적은 애플리케이션을 사전에 테스트하여 성능 문제를 식별하고 해결하는 것입니다. 다음 섹션에서는 성능 평가에 대해 자세히 알아보겠습니다. 효과적인 전략 구축, 평가 기법 습득 및 실용적인 가이드를 논의할 것입니다. 다음과 같은 내용을 기대할 수 있습니다:

- [효과적인 전략 구축](#효과적인-전략-구축)
- [평가 기법 습득](#평가-기법-습득)
- [How-To 가이드](#How-To-가이드)

## 효과적인 전략 구축

각 애플리케이션은 사용자 수, 트랜잭션 볼륨, 예상 응답 시간과 같은 고유한 특성을 가지고 있습니다. 따라서 평가 중인 특정 애플리케이션에 맞춤화된 효과적인 평가 전략을 수립하는 것이 중요합니다.

테스트를 시작하기 전에 테스트할 측면과 사용할 방법을 결정하는 것이 포함된 전략을 개요로 작성해야 합니다. 이 섹션에서는 이러한 고려 사항에 대해 자세히 논의합니다.

### 평가할 내용 식별

먼저 무엇을 테스트할지 정의하는 것으로 시작해 보겠습니다. 예를 들어, 애플리케이션이 완전히 구현되어 프로덕션과 유사한 환경에서 실행 중인 경우, 포괄적인 부하 테스트를 수행할 수 있습니다. 이를 통해 애플리케이션이 출시되기 전에 성능을 측정하고 사용자 경험을 예측할 수 있습니다.

전체 애플리케이션을 테스트하는 것은 사용자가 애플리케이션과 상호 작용할 때 경험하는 응답 시간을 가깝게 모사하는 측정치를 제공하기 때문에 좋은 아이디어입니다. 그러나 대형 언어 모델 (LLM) 앱의 사용자 상호 작용에는 여러 요소가 포함됩니다. 이에는 애플리케이션 프론트엔드, 백엔드, 네트워킹, LLM 모델 및 데이터베이스 및 AI 서비스와 같은 기타 클라우드 서비스가 포함됩니다.

이는 특히 현대적인 애플리케이션 아키텍처에 대해서도 마찬가지입니다. 이를 통해 전체 애플리케이션이 완료되고 배포 준비가 완료되기 전에 특정 서비스의 성능 테스트를 수행할 수 있습니다. 예를 들어, 애플리케이션에서 사용할 대형 언어 모델 (LLM)의 성능을 준비되기 전에 미리 테스트할 수 있습니다. [평가 기법 습득](#평가-기법-습득) 섹션에서는 Azure OpenAI 서비스에 배포된 모델의 성능을 테스트하는 방법을 알아볼 수 있습니다.

이제 여러 서비스가 함께 작동하여 사용자에게 응답을 생성하는 애플리케이션 아키텍처의 예를 살펴보겠습니다.

**검색 증강 생성** (RAG)은 ChatGPT와 같은 대형 언어 모델 (LLM) 애플리케이션 개발에서 자주 사용되는 아키텍처 패턴입니다. 콘텍스트 데이터를 제공하는 데 중요한 검색 단계를 LLM을 호출하기 전에 포함합니다. [Enterprise RAG 아키텍처](https://aka.ms/gpt-rag)는 기업 환경에서 구현된 RAG 패턴의 실용적인 예를 제공합니다. [How-To 가이드](#How-To-가이드) 섹션에서는 RAG 패턴을 기반으로 한 LLM 애플리케이션에 대한 부하 테스트를 수행하는 방법을 살펴볼 수 있습니다.

![아키텍처 개요](../media/perftest-GPT-RAG-Basic-communication.png)
<p align="center"><i>RAG 패턴을 기반으로 한 LLM 앱 구성 요소 간의 통신 예시.</i></p>

> 참고: 다이어그램을 간단하게 유지하기 위해 반환 메시지를 표시하지 않았습니다.

이 그림은 RAG를 기반으로 한 LLM 애플리케이션 내에서의 오케스트레이션 플로우를 보여줍니다.

다음은 작동 방식입니다:

1) 사용자가 프론트엔드 UI를 사용하여 질문을 제기합니다.
2) 프론트엔드 서비스가 사용자의 질문을 오케스트레이터로 전달합니다.
3) 오케스트레이터가 데이터베이스에서 사용자의 대화 기록을 검색합니다.
4) 오케스트레이터가 Key Vault에 저장된 AI 검색 키에 액세스합니다.
5) 오케스트레이터가 AI 검색 인덱스에서 관련 문서를 검색합니다.
6) 오케스트레이터가 Azure OpenAI를 사용하여 사용자 응답을 생성합니다.

이 프로세스의 각 단계는 다양한 서비스 간의 데이터 전송 및 처리를 수반하며, 모두 총 응답 시간에 기여합니다. 이러한 시나리오에서는 전체 애플리케이션 응답 시간뿐만 아니라 Azure OpenAI 모델 배포의 응답 시간과 같은 개별 구성 요소의 성능도 평가할 수 있습니다.

최종적으로 테스트 범위는 각 애플리케이션의 특정 요구 사항에 따라 달라집니다. 예를 들어, 내부 애플리케이션과 공개 애플리케이션은 서로 다른 성능 요구 사항을 가질 수 있습니다. 내부 HR 애플리케이션에서 15초의 응답 시간은 허용 가능할 수 있지만, 수백 명의 사용자가 있는 연락처 센터 앱은 높은 사용자 요구와 SLA로 인해 훨씬 더 빠른 응답이 필요할 수 있습니다. 성능 평가를 시작하기 전에 애플리케이션의 요구 사항을 알고 있도록 해야 합니다.

### 테스트 시나리오

평가할 내용을 정의한 후, 테스트 시나리오를 정의하는 것이 매우 중요합니다. 이 시나리오는 Playground에서 완성이 나타나는 데 걸리는 시간, Prompt Flow의 흐름이 실행되는 데 걸리는 시간 또는 VS Code에서 코드 실행 시간과 같지 않을 것입니다. 이러한 메트릭은 특정 구성 요소에서 잠재적인 성능 병목 현상을 식별하는 데 도움이 될 수 있지만, 실제 세계의 조건에서 성능을 평가하려면 테스트 시나리오는 실제 사용량 부하를 모방해야 합니다.

성능 테스트에서는 측정할 내용을 결정한 후, 테스트 시나리오를 정확하게 정의하는 것이 매우 중요합니다. 이 시나리오는 모델 플레이그라운드에서 완성이 나타나는 데 걸리는 시간이나 Prompt Flow의 실행 시간과 같은 것보다 간단하지 않을 것입니다. 이러한 메트릭은 단일 사용자의 경험을 기반으로 하기 때문에 특정 구성 요소에서 잠재적인 성능 병목 현상을 식별하는 데 도움이 될 수 있지만, 실제 세계의 조건에서 성능을 정확히 이해하기 위해서는 테스트 시나리오가 애플리케이션의 실제 사용 부하를 모방해야 합니다.

먼저 애플리케이션에 부과될 부하를 결정해야 합니다. 이 부하는 초당 요청 수와 같은 특정 시간 프레임 내에서 애플리케이션이 받을 요청 수로 정의됩니다.

예상되는 부하를 추정하는 여러 가지 방법이 있습니다. 애플리케이션이 이미 운영 중인 경우 모니터링 도구에서 수집한 현재 사용 데이터를 참조할 수 있습니다. 다음 그림은 이 접근 방식을 설명합니다. LLM을 솔루션에 통합하여 사용량이 증가할 것으로 예상되는 경우 예상된 성장을 수용하기 위해 추정된 부하를 조정해야 합니다.

![시간당 사용자 수](../media/perftest-users-per-hour.png)
<p align="center"><i>사용 시나리오 예시, 10시부터 13시까지의 최대 부하를 참조하세요.</i></p>

새로운 애플리케이션을 다룰 때는 벤치마킹이나 사용 모델링을 통해 예상되는 부하를 추정할 수 있습니다. 벤치마킹은 동일한 대상 사용자를 대상으로 하는 유사한 애플리케이션과 비교하는 것을 의미합니다. 사용 패턴을 연구함으로써 애플리케이션의 예상 부하에 대한 대략적인 추정치를 얻을 수 있습니다.

반면 사용 모델링은 애플리케이션의 예상 사용 패턴을 모델링하는 것을 의미합니다. 이는 애플리케이션이 개발되는 특정 분야의 이해 관계자 또는 잠재적 사용자와 상호 작용하여 달성할 수 있습니다. 그들의 통찰력은 애플리케이션이 어떻게 사용될 수 있는지에 대한 더 나은 이해를 제공할 수 있으며, 이는 분당 요청 수 (RPM)를 추정하는 데 도움이 될 수 있습니다.

애플리케이션 사용을 모델링하는 한 가지 방법은 **총 사용자 수**를 식별하는 것입니다. 이는 애플리케이션의 등록된 또는 잠재적 사용자를 모두 포함해야 합니다. 그런 다음 이러한 **사용자** 중 **피크 사용 시간**에 활동하는 **사용자**의 수를 식별합니다. 피크 사용 시간에 초점을 맞추는 것은 특히 고사용 기간이 명확한 시스템의 경우 시스템 성능을 정확하게 반영하지 않을 수 있는 비 피크 데이터에 대한 정확한 시스템 성능을 반영하지 않을 수 있습니다.

다음으로, 피크 시간 동안 사용자가 애플리케이션을 사용하는 평균 횟수를 추정합니다. 이를 **세션**이라고 합니다. 또한 사용자가 세션 동안 수행하는 작업 또는 **상호 작용**의 수를 추정합니다. 각 상호 작용은 애플리케이션에 대한 요청에 해당합니다.

예를 들어, 쇼핑몰용 모바일 앱을 고려해 보겠습니다. 사용자는 단일 세션에서 여러 상호 작용을 수행할 가능성이 높습니다. 먼저 추천된 레스토랑을 검색한 다음 특정 레스토랑의 운영 시간에 대해 물어볼 수 있습니다. 이러한 각 작업은 상호 작용입니다.

<p align="center">
  <img src="../media/perftest-sample-sequence-diagram.png" alt="샘플 사용자 세션" style="width:60%;">
  <br>
  <i>사용자 세션의 예시.</i>
</p>

Once you have the number of users (**u**), the percentage (**p**) of them that will use the application during the peak usage hours (**n**), the number of user sessions (**s**), and the average number of interactions (**i**) they have with the application, you can use the following formula to derive the RPM, considering these are sufficient to run a load test.

`RPM = (u * p * s * i) / n / 60`

Taking the previous example of the Mall App, let's consider a set of **10,000 registered users** on the App. We expect that during peak hours, **10%** of the users will interact at least once with the application to obtain information, such as store locations or product details.

In this case, we have:
- **u=10000** (total users)
- **p=0.1** (percentage of active users)
- **s=1** (sessions per user)
- **i=2** (interactions per session)
- **n=1** (network calls per interaction)

Therefore, the expected throughput for the peak hours is approximately **17 RPM**.

> **Note:** During testing, you may want to reproduce a load that is about 10% higher than estimated to be more conservative.

Defining the scenario for larger applications can become complex, requiring the identification of distinct user roles and their usage behavior. However, if exact modeling is challenging due to lack of information, just keep things simple, make an educated guess, and validate it with the application's stakeholders.

Another factor to consider when defining your test scenario is that LLM response times depend on the sizes of prompts and completions. Accurate testing requires replicating real usage scenarios, matching these sizes. For instance, RAG prompts are typically larger due to context text, while proofreading apps usually have similar prompt and completion sizes.

#### Test Data

Performance testing heavily relies on the data used during the test execution. It's crucial to use data that closely mirrors real-world scenarios. For instance, if we're testing an application like a copilot, the test results would be more accurate if each user asks different questions. Even if users ask the same questions, they should phrase them differently.

Consider a scenario where each virtual user asks the exact same question during the test. This could lead to results that don't accurately represent real-world usage. Certain components of the application might leverage caching mechanisms to deliver faster responses, skewing the results. Furthermore, the final metric, typically an average or a percentile, will be biased towards the repeated question.

Having experts in the App domain contribute to the creation of the test data set can greatly enhance its quality and relevance. Their knowledge can help shape more realistic and relevant examples. Alternatively, a Large Language Model (LLM) can be utilized to generate a synthetic dataset. This approach can be particularly useful for running tests, as it allows for the creation of diverse and comprehensive data scenarios. This practice not only enhances the quality of the tests but also ensures that they cover a wide range of potential real-world situations.

#### Test Measurements

Performance testing requires identifying key metrics. A common one is **Response Time**, the total time from sending a request to receiving a response. Performance requirements are typically determined by this metric, such as needing an average response time under ten seconds, or 95% of responses within ten seconds.

However, response time is not the only metric of interest. To gain a holistic understanding of the application's performance and the factors affecting it, we can categorize the metrics into two groups.

The first group comprises metrics that can be measured from the client's perspective - what the client can observe and capture. The second group consists of metrics that are measured by monitoring the server's performance. Let's explore each of these groups in detail:

##### Client metrics

When testing an LLM App, we usually obtain the following client metrics:

| Metric                     | Description                                        |
|----------------------------|----------------------------------------------------|
| Number of Virtual Users    | This metric shows the virtual user count during a load test, helping assess application performance under different user loads. |
| Requests per Second        | This is the rate at which requests are sent to the LLM App during the load test. It's a measure of the load your application can handle. |
| Request Response Time      | This is the time taken by the application to respond to a request.|
| Number of Failed Requests  | This is the count of requests that failed during the load test. It helps identify the reliability of your application under stress. |

> **Note:** "Request response time" is the duration from when a client sends a request to when it receives the full response, often called "end-to-end response time". It includes all processing time within the system. However, the "request response time" only measures the interval between sending a request and receiving the response. It does not account for any client-side processing time, such as rendering a webpage or executing JavaScript in a web application.

The diagram below illustrates how processing and communication times add up to the total response time. In the figure, each Tn marks a specific time during processing. T1 is when the user initiates a request through a client, such as a browser or MS Teams. T10 is when the user gets the full response. Note that the total response time (from T1 to T10) depends on the processing and response times of all components involved in the request.

![Users per hour](../media/perftest-response-time.png)
<p align="center"><i>Simplified example of the breakdown of request response time.</i></p>


###### Performance Metrics for a LLM

When conducting performance testing directly on a specific service, we can collect specific client-side metrics for the target service. In the context of performance testing a Language Model (LLM), we should consider metrics related to prompt tokens and response tokens. For instance, consider the deployment of an OpenAI model on Azure. The following table presents some of these metrics, which offer valuable insights into the client's interaction with the model deployment and its performance under load.

| Metric                           | Description                                                  |
|----------------------------------|--------------------------------------------------------------|
| Number Prompt Tokens per Minute  | Rate at which the client sends prompts to the OpenAI model.  |
| Number Generated Tokens per Min  | Rate at which the OpenAI model generates response tokens.    |
| Time to First Token (TTFT)       | Latency from the start of the client's request until the first response token is generated. |
| Time Between Tokens (TBT)        | Time interval between consecutive response tokens being generated. |

> **Note:** To examine the time intervals between tokens in Azure OpenAI's responses, you can utilize its streaming feature. Unlike conventional API calls that deliver the entire response at once, streaming sends each token or a set of tokens to the client as soon as they are produced. This allows for real-time performance monitoring and detailed analysis of the dynamics of response generation.

The diagram below provides a simplified view of a client's interaction with a model endpoint. The interaction commences at the moment (`T0`) when the client sends a request to the model's endpoint. The model responds in streaming mode, with `T1`, `T2`, and `TN` representing the moments when the first, second, and last tokens are received, respectively. 

In this scenario, we define several key metrics: **Time to First Token (TTFT)** is `T1 - T0`, **Time Between Tokens (TBT)** is `T2 - T1`, and the **end-to-end response time** is `TN - T0`. It's important to note that in streaming mode, the model's responses can arrive in multiple parts, each with several tokens. This makes both the diagram and the metrics an approximate representation of real-world scenarios.

![Users per hour](../media/perftest-aoai-response-time.png)
<p align="center"><i>AOAI deployment response in streaming mode.</i></p>


##### Server metrics

During performance testing, we focus on two types of metrics. The first type is client metrics, which directly affect the user experience. The second type is server metrics, which give us insights into the performance of server components.

Server metrics encompass a wide range of measurements. For instance, we might look at the CPU and memory usage of the application service running the frontend. We could also monitor the utilization of resources like the Azure OpenAI PTU deployment. These are just a few examples; there are many other server metrics we could potentially examine.

By collecting these measurements, we can create a detailed performance profile of the entire solution. This profile helps us identify any bottlenecks and tune any components that are not performing optimally.

LLM Apps consist of various services, and the server metrics we utilize will vary based on these services. To give you an idea, here are some examples of the metrics we might gather, depending on the specific service in use:

<!-- https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/monitoring -->

| Service Name             | Metric                           | Description |
|--------------------------|----------------------------------|-------------|
| Azure OpenAI   | Azure OpenAI Requests            | Total calls to Azure OpenAI API. |
| Azure OpenAI   | Generated Completion Tokens      | Output tokens from Azure OpenAI model. |
| Azure OpenAI   | Processed Inference Tokens       | The number of input and output tokens that are processed by the Azure OpenAI model. |
| Azure OpenAI   | Provision-managed Utilization V2 | The percentage of the provisioned-managed deployment that is currently being used. |
| Azure App Service | CPU Percentage                   | The percentage of CPU used by the App backend services. |
| Azure App Service | Memory Percentage                | The percentage of memory used by the App backend services. |
| Azure Cosmos DB          | Total Requests                   | Number of requests made to Cosmos DB. |
| Azure Cosmos DB  | Provisioned Throughput           | The amount of throughput that has been provisioned for a container or database. |
| Azure Cosmos DB  | Normalized RU Consumption        | The normalized request unit consumption based on the provisioned throughput. |
| Azure API Management     | Total Requests                   | Total number of requests made to APIM. |
| Azure API Management     | Capacity                         | Percentage of resource and network queue usage in APIM instance |


### When should I evaluate performance?

You might be wondering when to execute performance tests. To help us in this discussion, let's take a look at the Enterprise LLM Lifecycle, illustrated in the following image.

![LLM Lifecycle](../media/perftest-llmlifecycle.png)
<p align="center"><i>Enterprise LLM Lifecycle.</i></p>

The Enterprise LLM Lifecycle with Azure AI involves ideating and exploring, building and augmenting, operationalizing, and managing loops to develop, enhance, deploy, and govern large language model (LLM) applications. You can learn more about the Enterprise LLM Lifecycle by reading this blog: [Building for the future: The enterprise generative AI application lifecycle with Azure AI](https://azure.microsoft.com/es-es/blog/building-for-the-future-the-enterprise-generative-ai-application-lifecycle-with-azure-ai/).

Performance testing is crucial and should start as early as possible during the development process. This early start provides enough time for making necessary adjustments and optimizations. The exact timing, however, depends on what aspects of the application you're testing.

If your goal is to evaluate the performance of the entire LLM App before it's used by end-users, the application must be fully developed and deployed to a staging environment. Typically, this load testing of the LLM App occurs during the initial iterations of the Operationalization loop in the Enterprise LLM Lifecycle.

Keep in mind that there are scenarios where performance evaluations can be conducted before Operationalization. For instance, during the Experimenting and Ideating phase, you might be exploring various LLMs for use. If you're considering using one of the models available on Azure OpenAI, this could be an excellent time to conduct a performance benchmark test using the Azure OpenAI benchmarking tool.

The following figure illustrates the moments in the LLM lifecycle where the two types of performance tests mentioned earlier are usually conducted.

![LLM Lifecycle](../media/perftest-llmlifecycle-with-tests.png)
<p align="center"><i>Performance tests in the LLM Lifecycle.</i></p>

## Mastering Evaluation Techniques

OK, if you've reached this point, you already know what's important to consider in your testing strategy. Here, we will explore two evaluation techniques. One is aimed at performance testing of the entire LLM application, and the second is more focused on testing the deployed LLM. It's worth mentioning that these are two commonly used examples, but this is a non-exhaustive list. Depending on your performance requirements, it may be necessary to use other techniques in your testing strategy.

#### LLM App Load Testing

Azure Load Testing is a fully managed load-testing service that enables you to generate high-scale LLM App load testing. The service simulates traffic for your applications, regardless of where they're hosted. You can use it to test and optimize application performance, scalability, or capacity of your application. You have the flexibility to create and execute load tests either through the Azure portal or via the Azure Command Line Interface (CLI), managing and running your tests in the way that suits you best.

Azure Load Testing helps you simulate a large number of users sending requests to a server to measure how well an application or service performs under heavy load. You can use Apache JMeter scripts to set up and run these tests. These scripts can act like real users, doing things like interacting with the service, waiting, and using data. In the [How-To Guides](#how-to-guides) section, you will find a guide on how you can test your LLM App with a practical example.

The diagram below shows the high-level architecture of Azure Load Testing. It uses JMeter to simulate heavy server loads and provides detailed performance metrics. You can adjust the number of test engine instances to meet your load test requirements, making the system scalable and robust.

![LLM Lifecycle](../media/perftest-azure-load-testing.png)
<p align="center"><i>Azure Load Testing.</i></p>

LLM App load testing is crucial for identifying performance issues and ensuring that your application and its Azure dependencies (like the App Service, Function App, and Cosmos DB) can handle peak loads efficiently.

The following table offers an explanation of important concepts associated with Azure Load Testing. Grasping these concepts is essential for effectively using Azure's load testing features to evaluate the performance of the LLM App under various load scenarios.

| Concept | Description |
|---------|-------------|
| Test | Refers to a performance evaluation setup that assesses system behavior under simulated loads by configuring load parameters, test scripts, and target environments. |
| Test Run | Represents the execution of a Test.|
| Test Engine | Engine that runs the JMeter test scripts. Adjust load test scale by configuring test engine instances. |
| Threads | Are parallel threads in JMeter that represent virtual users. They are limited to a maximum of 250. |
| Virtual Users (VUs) | Simulate concurrent users. Calculated as threads * engine instances. |
| Ramp-up Time | Is the time required to reach the maximum number of VUs for the load test. |
| Latency | Is the time from sending a request to the moment the beginning of the response arrives. |
| Response Time | This refers to the duration between sending a request and receiving the full response. It does not include any time spent on client-side response processing or rendering. |

Azure Load Testing allows parameter definition, including environment variables, secrets, and certificates. It supports test scaling, failure criteria setting, and monitoring of application components and resource metrics. CSV files with test data and JMeter configurations can be uploaded for flexible, customizable test scripts.

For [secure access](https://learn.microsoft.com/en-us/azure/load-testing/how-to-test-secured-endpoints) to Azure Key Vault [secrets](https://learn.microsoft.com/en-us/azure/load-testing/how-to-parameterize-load-tests#secrets), a managed identity can be used. This resource, when deployed within your [virtual network](https://learn.microsoft.com/en-us/azure/load-testing/how-to-test-private-endpoint), It is capable of generating load directed at your application's private endpoint. Authentication via access tokens, user credentials, or client certificates is also supported, depending on your application's requirements.

##### Monitoring Application Resources

With Azure Load Testing, you can monitor your server-side performance during load tests. You can specify which Azure application components to monitor in the test configuration. You can view these server-side metrics both during the test and afterwards on the load testing dashboard. The following figure shows an example of server-side metrics obtained from an App Service after running a test. You can see the Azure services from which you can obtain server-side metrics [in this link](https://learn.microsoft.com/en-us/azure/load-testing/resource-supported-azure-resource-types).

![Server metrics](../media/perftest-server-metrics.png)
<p align="center"><i>Azure Load Testing Server-side Performance Metrics.</i></p>

##### Load Testing Automation

Integrating Azure Load Testing into your CI/CD pipeline is a key step in enhancing your organization's adoption of LLMOps practices. This integration enables automated load testing, ensuring consistent performance checks at crucial points in the development lifecycle. You can trigger Azure Load Testing directly from Azure Pipelines or GitHub Actions workflows, providing a simplified and efficient approach to performance testing. Below are some examples of commands to automate the creation and execution of a load test.

```
# Example command to create a load test 
az loadtest create \
  --name $loadTestResource \
  --resource-group $resourceGroup \
  --location $location \
  --test-file @path-to-your-jmeter-test-file.jmx \
  --configuration-file @path-to-your-load-test-config.yaml
```

```
# Example command to run the load test
az loadtest run \
  --name $loadTestResource \
  --resource-group $resourceGroup \
  --test-id $testId
```

For more information on configuring a load test and automating these steps using the Azure Command Line Interface (CLI), refer to the [Azure Load Testing CI/CD configuration guide](https://learn.microsoft.com/en-us/azure/load-testing/how-to-configure-load-test-cicd) and the [Azure CLI reference for load testing](https://learn.microsoft.com/en-us/cli/azure/load).

##### Key Metrics to Monitor During Load Tests

When conducting load tests, it's crucial to monitor certain key metrics to understand how your application performs under stress. These metrics will help you identify any potential bottlenecks or areas that need optimization. Here are some of the most important ones to keep an eye on:

   - **Request Rate**: Monitor the request rate during load testing. Ensure that the LLM application can handle the expected number of requests per second.
   - **Response Time**: Analyze response times under different loads. Identify bottlenecks and optimize slow components.
   - **Throughput**: Measure the number of successful requests per unit of time. Optimize for higher throughput.
   - **Resource Utilization**: Monitor CPU, memory, and disk usage. Ensure efficient resource utilization.

##### Best Practices for Executing Load Tests

To ensure your load tests are effective and yield meaningful insights, it's worthwhile to review the following recommendations. Here are some key strategies to consider:

   - **Test Scenarios**: Create realistic test scenarios that mimic actual user behavior.
   - **Ramp-Up Strategy**: Gradually increase the load to simulate real-world traffic patterns. The warm-up period typically lasts between 20 to 60 seconds. After the warm-up, the actual load test begins
   - **Think Time**: Include think time between requests to simulate user interactions.
   - **Geographical Distribution**: Test from different Azure regions to assess global performance.

##### Performance Tuning Strategies for LLM Apps

This section discusses performance tuning for LLM Apps. Application performance is heavily influenced by design and architecture. Effective structures can manage high loads, while poor ones may struggle. We'll cover various performance tuning aspects, not all of which may be universally applicable.

###### Application Design

- **Optimize Application Code**: Examine and refine the algorithms and backend systems of your LLM application to increase efficiency. Utilize asynchronous processing methods, such as Python's async/await, to elevate application performance. This method allows data processing without interrupting other tasks.

- **Batch Processing**: Batch LLM requests whenever possible to reduce overhead. Grouping multiple requests for simultaneous processing improves throughput and efficiency by allowing the model to better leverage parallel processing capabilities, thereby optimizing overall performance.

- **Implement Caching**: Use caching for repetitive queries to lighten the application's load and speed up response times. This is particularly useful in LLM applications where similar questions are common. Caching answers to frequently asked or common questions reduces the need to run the model repeatedly for the same inputs, saving both time and computational resources.

- **Revisit your Retry Logic**: LLM model deployments might start to operate at their capacity, which can lead to 429 errors. A well-designed retry mechanism can help maintain application responsiveness. With the OpenAI Python SDK, you can opt for an exponential backoff algorithm. This algorithm gradually increases the wait time between retries, helping to prevent service overload. Additionally, consider the option of falling back on another model deployment. For more information, refer to the load balance item in the Solution Architecture section.

###### Prompt Design

- **Generate Less Tokens**: To reduce model latency, create concise prompts and limit token output. [cutting 50% of your output tokens may cut ~50% your latency](https://platform.openai.com/docs/guides/latency-optimization). Utilizing 'max_tokens' can also expedite the response time.

- **Optimize Your Prompt**: If dealing with large amounts of context data, consider prompt compression methods. Approaches like those offered by [LLMLingua-2](https://llmlingua.com/llmlingua2.html), fine-tuning the model to reduce lengthy prompts, eliminating superfluous RAG responses, and removing extraneous HTML can be efficient. Trimming your prompt by 50% might only yield a latency reduction of 1-5%, but these strategies can lead to more substantial improvements in performance.

- **Refine Your Prompt**: Optimize the shared prompt prefix by placing dynamic elements, such as RAG results or historical data, toward the end of your prompt. This enhances compatibility with the [KV cache system](https://arxiv.org/pdf/2211.05102) commonly used by most large language model providers. As a result, fewer input tokens need processing with each request, increasing efficiency.

- **Use Smaller Models**: Whenever possible, pick smaller models because they are faster and more cost-effective. You can improve their responses by using detailed prompts, a few examples, or by fine-tuning.

###### Solution Architecture

- **Provisioned Throughput Deployments**: Use these in scenarios requiring stable latency and predictable performance, avoiding the 'noisy neighbor' issue in regular pay-as-you-go setups.

- **Load Balancing LLM Endpoints**: Implement [load balancing](https://github.com/Azure-Samples/openai-aca-lb/) for LLM deployment endpoints. Distribute the workload dynamically to enhance performance based on endpoint latency. Establish suitable rate limits to prevent resource exhaustion and ensure stable latency.

- **Resource Scaling**: If services show strain under increased load, consider scaling up resources. Azure allows seamless scaling of CPU, RAM, and storage to meet growing demands.

- **Network Latency**: Position Azure resources, like the Azure OpenAI service, near your users geographically to minimize network latency during data transmission to and from the service.

#### Azure OpenAI Benchmarking

The [Azure OpenAI Benchmarking Tool](https://github.com/Azure/azure-openai-benchmark) enables you to assess the performance of Azure OpenAI deployments and choose the ideal model and deployment approach (PTU vs. pay-as-you-go) for your specific needs. It provides detailed latency metrics and simulates various traffic patterns. This tool is particularly useful during model selection and experimentation in the initial phases of a project, as it assists developers in determining whether the model deployment is appropriately sized or if it needs adjustments. This tool allows you to make data-driven decisions, ensuring your deployment is both efficient and tailored to your operational requirements.

The benchmarking tool works by creating traffic patterns that mirror the expected test load. When conducting the test, make sure it runs long enough for the throughput to reach a stable state, especially when the utilization is close to or at 100%. The benchmark tool also generates synthetic requests with random words, matching the number of context tokens in the requested shape profile. To simulate a worst-case scenario and prevent unrealistically favorable results due to optimizations, each prompt includes a random prefix, forcing the engine to fully process each request.

This type of test is especially beneficial for Provisioned-Managed deployments. By adjusting the number of provisioned throughput units (PTUs) deployed, you can optimize your solution's design. Based on the analysis, you might need to revise the number of PTU deployments or even consider a hybrid architecture with PTU and Pay-as-you-go deployments, using load balancing between two or more deployments.

###### Test Parameters

You can configure the benchmarking tool to optimize your load testing experience with several adjustable parameters:

 With the `rate` parameter, you can control the frequency of requests in Requests Per Minute (RPM), allowing for detailed management of test intensity.

The `clients` parameter enables you to specify the number of parallel clients that will send requests simultaneously, providing a way to simulate varying levels of user interaction.

The `shape-profile` parameter, with options like "balanced", "context", "custom" or "generation", adjusts the request characteristics based on the number of context and generated tokens, enabling precise testing scenarios that reflect different usage patterns. 

When shape-profile is set to "custom", two additional parameters come into play: context-token and max-tokens. The `context-token` parameter allows you to specify the number of context tokens in the request, while `max-tokens` allows you to specify the maximum number of tokens that can be generated in the response. 

The `aggregation-window` parameter defines the duration, in seconds, for which the data aggregation window spans. Before the test hits the aggregation-window duration, all stats are computed over a flexible window, equivalent to the elapsed time. This ensures accurate RPM/TPM stats even if the test ends early due to hitting the request limit.

###### Retry Strategy

The `retry` parameter allows you to set the retry strategy for requests, offering options such as "none" or "exponential", which can be crucial for handling API request failures effectively. When setting up a retry strategy for Azure OpenAI benchmarking, it's crucial to select an approach that carefully balances resource capacity to avoid skewing latency statistics. 

When running a test with retry=none, throttled requests are immediately retried with a reset start time, and latency metrics only reflect the final successful attempt, o que pode nao representar a experiencia do usuario final . Use this setting for workloads within resource limits without throttling or to assess how many requests need redirecting to a backup during peak loads that surpass the primary resource’s capacity.

 Conversely, with retry=exponential, failed or throttled requests are retried with exponential backoff, up to 60 seconds. This approach, recommended when no backup resources are deployed, measures the total time from the first failed attempt to the successful completion, thus capturing the full potential wait time an end-user might experience. This method is ideal for understanding the impacts of throttling and retries on total request latency, especially in scenarios like chat applications where response times are critical.

###### Sample Scenario

In the following example, taken from the tool's documentation, the benchmarking tool tests a traffic pattern that sends requests to the gpt-4 deployment in the 'myaccount' Azure OpenAI resource at a rate of 60 requests per minute, with the retry set to exponential. The default traffic shape is used, where each request contains 1000 context tokens, and the maximum response size is limited to 500 tokens.
```
$ python -m benchmark.bench load \
    --deployment gpt-4 \
    --rate 60 \
    --retry exponential \
    https://myaccount.openai.azure.com

2023-10-19 18:21:06 INFO     using shape profile balanced: context tokens: 500, max tokens: 500
2023-10-19 18:21:06 INFO     warming up prompt cache
2023-10-19 18:21:06 INFO     starting load...
2023-10-19 18:21:06 rpm: 1.0   requests: 1     failures: 0    throttled: 0    ctx tpm: 501.0  gen tpm: 103.0  ttft avg: 0.736  ttft 95th: n/a    tbt avg: 0.088  tbt 95th: n/a    e2e avg: 1.845  e2e 95th: n/a    util avg: 0.0%   util 95th: n/a   
2023-10-19 18:21:07 rpm: 5.0   requests: 5     failures: 0    throttled: 0    ctx tpm: 2505.0 gen tpm: 515.0  ttft avg: 0.937  ttft 95th: 1.321  tbt avg: 0.042  tbt 95th: 0.043  e2e avg: 1.223 e2e 95th: 1.658 util avg: 0.8%   util 95th: 1.6%  
2023-10-19 18:21:08 rpm: 8.0   requests: 8     failures: 0    throttled: 0    ctx tpm: 4008.0 gen tpm: 824.0  ttft avg: 0.913  ttft 95th: 1.304  tbt avg: 0.042  tbt 95th: 0.043  e2e avg: 1.241 e2e 95th: 1.663 util avg: 1.3%   util 95th: 2.6% 
```

When you run the test, you will obtain average and 95th percentile metrics from the following measures:

|measure|description|
|-|-|
|`ttft`| Time to First Token. Time in seconds from the beginning of the request until the first token was received.|
|`tbt`| Time Between Tokens. time in seconds between two consecutive generated tokens.|
|`e2e`| End to end request time.|
|`util`| Azure OpenAI deployment utilization percentage as reported by the service.|


##### Monitoring AOAI Resource

To leverage Azure Monitor Log Analytics queries for log and metrics analysis, it's essential to [configure diagnostic settings](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/monitoring#configure-diagnostic-settings) for both your Azure OpenAI resource and Log Analytics workspace. While platform metrics and the Azure Monitor activity log are automatically collected and stored, Azure Monitor resource logs require a diagnostic setting to be created and routed to one or more locations.

Configuring diagnostic settings for Azure OpenAI Service is essential for monitoring the availability, performance, and operation of your Azure resources. These settings enable the collection and analysis of metrics and log data from your Azure OpenAI resource. They allow you to track key metrics like the number of API calls, generated tokens, and training hours, and provide access to logs for insights into resource-specific activities. Proper configuration of these settings offers valuable insights into your Azure OpenAI resources' performance and usage, aiding in application optimization and troubleshooting.

After configuring the diagnostic settings, you can start querying the generated logs. Simply access your Azure OpenAI resource in the portal and then select Logs in the Monitoring section. Next, click on the Log Analytics Workspace that you selected during the diagnostic settings configuration and select the workspace's Logs option.

Below is a query example that retrieves logs from AzureDiagnostics for "ChatCompletions_Create" operations, conducted between 3:30 PM and 4:30 PM on April 26, 2024. It selects logs with details such as timestamp, resource, operation, duration, response code, and additional properties, enabling a detailed analysis of the operation's performance and outcomes during that hour.

```
AzureDiagnostics
| where TimeGenerated between(datetime(2024-04-26T15:30:00) .. datetime(2024-04-26T16:30:00))
| where OperationName == "ChatCompletions_Create"
| project TimeGenerated, _ResourceId, Category, OperationName, DurationMs, ResultSignature, properties_s
```

![Server metrics](../media/perftest-azure-diagnostics.png)
<p align="center"><i>Analyzing Azure OpenAI Metrics with Azure Monitor.</i></p>


## How-To Guides

Now that you understand the concepts for conducting performance tests, you can refer to the following sections where we provide a detailed guide on how to use the tools mentioned in the text to test your LLM App or your Azure OpenAI model deployment.

- [LLM RAG application testing with Azure Load Testing](https://github.com/Azure/GPT-RAG/blob/main/docs/LOAD_TESTING.md).

- [Model deployment testing with AOAI Benchmarking Tool](AOAI_BENCH_TOOL.md).

<!--  references:
https://www.microsoft.com/en-us/research/group/experimentation-platform-exp/articles/how-to-evaluate-llms-a-complete-metric-framework/ 
https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/monitoring
https://jmeter.apache.org/usermanual/glossary.html -->