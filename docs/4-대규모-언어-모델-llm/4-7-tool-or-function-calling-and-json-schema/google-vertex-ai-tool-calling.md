---
title: "Google Vertex AI Tool Calling"
date: "2025-07-06"
tags: ["LLM", "Tool Calling", "Vertex AI", "Gemini"]
difficulty: "medium"
---

# Google Vertex AI Tool Calling

## 1. 핵심 개념 (Core Concept)

Google Vertex AI의 Tool Calling(Function Calling)은 Gemini와 같은 대규모 언어 모델(LLM)을 외부 시스템 및 API와 연결하는 기능입니다. 이를 통해 모델은 사전 훈련된 지식을 넘어 실시간 데이터에 접근하고 다른 서비스와 상호 작용하여 기능을 확장할 수 있습니다. 사용자의 프롬프트에 따라 모델이 필요한 외부 도구(함수)를 식별하면, 모델은 함수를 직접 실행하는 대신 호출해야 할 함수의 이름과 인수가 포함된 구조화된 JSON 객체를 반환하며, 실제 실행은 애플리케이션 코드에서 담당합니다.

---

## 2. 상세 설명 (Detailed Explanation)

Vertex AI Tool Calling은 모델이 외부 세계와 상호작용할 수 있도록 하는 강력한 메커니즘으로, 크게 함수 선언, 모델의 추론, 그리고 실제 실행 및 응답의 세 단계로 이루어집니다.

### 2.1 함수 선언 (Function Declaration)

개발자는 사용 가능한 도구(함수)의 명세(Specification)를 OpenAPI 스키마와 호환되는 형식으로 모델에 제공해야 합니다. 이 명세에는 함수의 이름, 기능에 대한 설명, 그리고 함수가 받는 매개변수(Parameters)가 포함됩니다. 모델은 이 정보를 바탕으로 언제, 어떻게 특정 도구를 사용해야 할지 판단합니다.

### 2.2 모델 추론 (Model Reasoning)

사용자가 프롬프트를 입력하면, 모델은 해당 요청을 처리하기 위해 미리 선언된 함수 중 하나를 호출해야 하는지 여부를 판단합니다. 만약 함수 호출이 필요하다고 판단되면, 모델은 함수를 직접 실행하지 않고, 대신 호출할 함수의 이름과 필요한 인수가 담긴 JSON 객체를 반환합니다. 이 과정은 모델의 추론 능력에 기반하여 이루어지며, 사용자의 의도를 가장 잘 충족시키는 도구를 선택합니다.

### 2.3 실행 및 응답 (Execution and Response)

모델이 반환한 JSON 객체를 받은 애플리케이션 코드는 명시된 함수를 실제로 실행하고, 그 결과를 다시 모델에 전달합니다. 모델은 이 함수 실행 결과를 바탕으로 사용자의 초기 프롬프트에 대한 최종적이고 더 정확한 답변을 생성합니다. 이처럼 실제 데이터나 외부 시스템과의 상호작용 결과를 반영하여, 모델은 훨씬 더 풍부하고 맥락에 맞는 응답을 할 수 있게 됩니다.

---

## 3. 예시 (Example)

### 코드 예시 (Python)

다음은 Vertex AI Gemini Pro를 사용하여 특정 지역의 날씨 정보를 가져오는 Tool Calling 예시입니다.

```python
import vertexai
from vertexai.generative_models import GenerativeModel, Tool

# 날씨 정보 조회를 위한 가상 함수
def get_current_weather(location: str, unit: str = "celsius"):
    """지정된 위치의 현재 날씨 정보를 가져옵니다."""
    # 실제 API 호출 대신 더미 데이터 반환
    return {"location": location, "temperature": "32", "unit": unit}

# Vertex AI 초기화
vertexai.init(project="your-project-id", location="us-central1")

# 사용할 도구(함수) 선언
weather_tool = Tool.from_functions([get_current_weather])

# 모델 생성
model = GenerativeModel("gemini-1.0-pro", tools=[weather_tool])

# 채팅 세션 시작
chat = model.start_chat()

# 프롬프트 전송
prompt = "서울의 현재 날씨는 어떤가요?"
response = chat.send_message(prompt)

# 모델이 함수 호출을 요청했는지 확인
if response.candidates[0].function_calls:
    function_call = response.candidates[0].function_calls[0]
    
    # 호출할 함수와 인자 확인
    if function_call.name == "get_current_weather":
        args = function_call.args
        
        # 함수 실행
        weather_info = get_current_weather(
            location=args["location"],
            unit=args.get("unit", "celsius")
        )
        
        # 함수 실행 결과를 모델에 다시 전송
        response = chat.send_message(
            Part.from_function_response(
                name="get_current_weather",
                response={"content": weather_info}
            )
        )

# 최종 응답 출력
print(response.text)
```

### 사용 사례 (Use Case)

*   **실시간 정보 조회**: 최신 날씨 예보, 주식 시세, 뉴스 기사 등 실시간 데이터에 접근하여 사용자에게 제공합니다.
*   **외부 시스템 연동**: 항공권 예약, CRM 시스템의 고객 정보 업데이트, 온라인 쇼핑몰 주문 처리 등 외부 서비스와 상호작용합니다.
*   **Retrieval-Augmented Generation (RAG)**: 내부 기술 문서나 데이터베이스를 조회하여 특정 질문에 대한 정확한 정보를 찾아 답변합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. Vertex AI의 Tool Calling이 기존의 LLM과 어떻게 다른가요?**
    *   **A.** 기존 LLM은 학습된 데이터 내에서만 정보를 처리할 수 있었지만, Tool Calling을 통해 외부 API나 시스템과 실시간으로 상호작용할 수 있습니다. 이는 모델이 정적인 지식을 넘어 동적인 데이터를 다루고, 실제 세계에서 특정 작업을 수행할 수 있게 해준다는 점에서 큰 차이가 있습니다.
*   **Q. Tool Calling 과정에서 모델은 함수를 직접 실행하나요? 만약 아니라면 그 이유는 무엇인가요?**
    *   **A.** 아니요, 모델은 함수를 직접 실행하지 않습니다. 대신 어떤 함수를 어떤 인자와 함께 호출해야 하는지에 대한 정보를 담은 JSON 객체를 반환합니다. 이는 보안과 제어의 측면에서 중요합니다. 실제 함수 실행은 개발자의 코드 환경에서 이루어지므로, 민감한 작업이나 시스템에 영향을 미치는 동작을 안전하게 통제할 수 있습니다.
*   **Q. 여러 개의 Tool을 정의했을 때, 모델은 어떤 기준으로 사용할 Tool을 선택하나요?**
    *   **A.** 모델은 사용자의 프롬프트와 각 Tool에 제공된 설명을 바탕으로 가장 적절한 Tool을 선택합니다. 모델의 추론 능력을 활용하여 사용자의 의도를 파악하고, 그 의도를 가장 잘 해결할 수 있는 함수를 결정합니다. 따라서 명확하고 상세한 함수 설명(description)을 작성하는 것이 매우 중요합니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Vertex AI Tool Calling 공식 문서](https://cloud.google.com/vertex-ai/docs/generative-ai/multimodal/function-calling)
*   [Google AI for Developers: Introduction to function calling](https://ai.google.dev/docs/function_calling)