---
title: OpenAI Function Calling
date: '2025-07-06'
tags: [LLM, Tool Calling, OpenAI, GPT]
difficulty: medium
---

# OpenAI Function Calling

## 1. 핵심 개념 (Core Concept)

OpenAI Function Calling은 GPT 모델이 외부 함수나 API를 호출하여 그 결과를 활용할 수 있도록 하는 기능입니다. 모델은 사용자의 질문에 답변하기 위해 외부 도구가 필요하다고 판단하면, 직접 함수를 실행하는 대신 어떤 함수를 어떤 인자로 호출해야 하는지에 대한 정보를 담은 JSON 객체를 생성합니다. 개발자는 이 정보를 바탕으로 실제 함수를 실행하고, 그 결과를 다시 모델에 전달하여 최종 답변을 생성하게 할 수 있습니다.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

OpenAI Function Calling은 LLM의 능력을 외부 세계로 확장시키는 핵심적인 기능으로, 다음과 같은 과정으로 동작합니다.

### 2.1 함수 정의 (Function Definition)

먼저 개발자는 모델이 사용할 수 있는 함수들의 목록과 각 함수의 명세(Specification)를 정의해야 합니다. 이 명세에는 함수의 이름, 기능에 대한 설명, 그리고 함수가 받는 매개변수(Parameters)가 JSON 스키마 형식으로 포함됩니다. 모델은 이 정보를 통해 각 함수의 용도와 사용법을 이해합니다.

### 2.2 모델의 함수 호출 결정

사용자가 프롬프트를 입력하면, 모델은 대화의 맥락과 사용자의 의도를 파악하여 정의된 함수 중 하나를 호출할 필요가 있는지 판단합니다. 만약 함수 호출이 필요하다고 결정되면, 모델의 응답 메시지에는 `tool_calls` 필드가 포함됩니다. 이 필드에는 호출할 함수의 이름과 전달할 인수가 담긴 JSON 객체가 들어 있습니다.

### 2.3 외부 함수 실행 및 결과 전달

애플리케이션 코드는 모델의 응답에서 `tool_calls` 정보를 추출하여 명시된 함수를 실제로 실행합니다. 함수 실행이 완료되면, 그 반환값을 새로운 메시지(role: `tool`)에 담아 대화 기록에 추가하고 다시 모델에 전달합니다. 이 과정을 통해 모델은 외부 함수의 실행 결과를 알게 됩니다.

### 2.4 최종 응답 생성

모델은 함수 실행 결과를 포함한 전체 대화 맥락을 바탕으로 사용자의 초기 질문에 대한 최종적이고 자연스러운 답변을 생성합니다. 이로써 모델은 단순한 텍스트 생성을 넘어, 외부 데이터 조회나 특정 작업 수행과 같은 동적인 역할을 수행할 수 있게 됩니다.

______________________________________________________________________

## 3. 예시 (Example)

### 코드 예시 (Python)

다음은 OpenAI API를 사용하여 사용자의 이메일 주소 형식이 유효한지 검사하는 Function Calling 예시입니다.

```python
from openai import OpenAI
import json

client = OpenAI(api_key="your-api-key")

def validate_email(email: str) -> dict:
    """주어진 이메일 주소의 형식이 유효한지 검사합니다."""
    if "@" in email and "." in email.split("@")[1]:
        return {"is_valid": True}
    return {"is_valid": False}

tools = [
    {
        "type": "function",
        "function": {
            "name": "validate_email",
            "description": "이메일 주소의 형식이 유효한지 검사합니다.",
            "parameters": {
                "type": "object",
                "properties": {
                    "email": {
                        "type": "string",
                        "description": "검사할 이메일 주소",
                    },
                },
                "required": ["email"],
            },
        },
    }
]

messages = [{"role": "user", "content": "내 이메일 주소가 유효한지 확인해줘: test@example.com"}]

response = client.chat.completions.create(
    model="gpt-4-turbo-preview",
    messages=messages,
    tools=tools,
    tool_choice="auto",
)

response_message = response.choices[0].message

# 모델이 함수 호출을 요청했는지 확인
if response_message.tool_calls:
    tool_call = response_message.tool_calls[0]
    function_name = tool_call.function.name
    function_args = json.loads(tool_call.function.arguments)

    if function_name == "validate_email":
        # 함수 실행
        function_response = validate_email(email=function_args.get("email"))

        # 대화 기록에 함수 호출과 결과 추가
        messages.append(response_message)  # 모델의 함수 호출 요청
        messages.append(
            {
                "tool_call_id": tool_call.id,
                "role": "tool",
                "name": function_name,
                "content": json.dumps(function_response),
            }
        )

        # 함수 실행 결과를 바탕으로 최종 응답 요청
        second_response = client.chat.completions.create(
            model="gpt-4-turbo-preview",
            messages=messages,
        )
        print(second_response.choices[0].message.content)

```

### 사용 사례 (Use Case)

- **챗봇 및 가상 어시스턴트**: "오늘 내 캘린더에 어떤 일정이 있어?"와 같은 질문에 답변하기 위해 구글 캘린더 API를 호출합니다.
- **데이터베이스 쿼리**: "지난 달 가장 많이 팔린 제품은 무엇인가요?"라는 질문을 자연어로 받아, 이를 SQL 쿼리로 변환하여 데이터베이스에서 정보를 조회합니다.
- **외부 서비스 제어**: 스마트 홈 기기를 제어하거나, 이메일을 보내거나, 소셜 미디어에 포스팅하는 등의 작업을 수행합니다.

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. OpenAI의 Function Calling과 다른 LLM의 Tool Calling 기능(예: Google Vertex AI)을 비교 설명해주세요.**
  - **A.** OpenAI Function Calling과 Google Vertex AI Tool Calling은 LLM이 외부 도구를 사용한다는 핵심 개념은 동일하지만, API 디자인과 구현 방식에서 차이가 있습니다. OpenAI는 `tools`와 `tool_choice` 파라미터를 사용하며, 모델 응답에 `tool_calls` 필드를 포함하는 반면, Vertex AI는 `Tool` 객체를 통해 함수를 선언하고 모델 응답의 `function_calls` 필드를 확인하는 방식을 사용합니다. 두 기능 모두 JSON 스키마 기반으로 함수 명세를 정의하는 등 유사한 점도 많습니다.
- **Q. Function Calling을 사용할 때 보안상 고려해야 할 점은 무엇인가요?**
  - **A.** 가장 중요한 것은 모델이 직접 함수를 실행하지 않는다는 점을 이해하는 것입니다. 실제 실행은 개발자의 통제 하에 이루어지므로, 데이터베이스를 수정하거나 외부 시스템에 영향을 미치는 중요한 함수를 호출하기 전에는 반드시 사용자 확인 단계를 거치거나, 안전한 환경에서 실행되도록 격리하는 것이 중요합니다. 또한, 악의적인 프롬프트를 통해 의도치 않은 함수가 호출되지 않도록 입력값을 신중하게 검증해야 합니다.
- **Q. Function Calling의 `tool_choice` 파라미터는 어떤 경우에 유용한가요?**
  - **A.** `tool_choice` 파라미터는 모델이 어떤 함수를 호출할지 개발자가 직접 제어하고 싶을 때 유용합니다. 예를 들어, 사용자의 특정 입력에 대해서는 반드시 특정 함수가 호출되도록 강제(`{"type": "function", "function": {"name": "my_function"}}`)하거나, 모델이 함수를 호출하지 않고 사용자에게 직접 답변하도록(`"none"`) 설정할 수 있습니다. 이를 통해 애플리케이션의 동작을 더 예측 가능하고 안정적으로 만들 수 있습니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [OpenAI Function Calling 공식 문서](https://platform.openai.com/docs/guides/function-calling)
- [OpenAI Cookbook: Function Calling 예제](https://cookbook.openai.com/examples/how_to_call_functions_with_chat_models)
