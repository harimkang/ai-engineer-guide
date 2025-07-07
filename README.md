# 🚀 AI 엔지니어 기술 지식 가이드

> AI 개발자가 알아야 할 필수 기술 지식을 체계적으로 정리한 한국어 기술 문서입니다.

[![Korean](https://img.shields.io/badge/Language-Korean-blue.svg)](https://github.com/harimkang/ai-engineer-guide)

---

## 📖 소개

이 프로젝트는 **AI 엔지니어**를 준비하는 분들을 위한 포괄적인 기술 지식 베이스입니다. CS 기초부터 최신 AI 기술까지, 면접에서 자주 등장하는 핵심 개념들을 체계적으로 정리했습니다.

### ✨ 특징

- 📚 **체계적인 구성**: CS 기초 → 딥러닝 → 컴퓨터 비전 → LLM → Agentic AI → MLOps 순으로 단계적 학습
- 🇰🇷 **한국어 중심**: 모든 문서가 한국어로 작성되어 이해하기 쉬움
- 🎯 **면접 중심**: 실제 면접에서 자주 나오는 질문과 개념 위주로 구성
- 📊 **시각적 자료**: Mermaid 다이어그램, 표, 이미지를 활용한 직관적인 설명
- 🔄 **지속적 업데이트**: 최신 AI 기술 트렌드를 반영한 꾸준한 내용 업데이트

---

## 📚 목차

> 💡 **빠른 탐색**: 각 섹션을 클릭하여 해당 문서로 바로 이동하세요!

### 🖥️ [1. CS 전공 지식](docs/1-cs-전공-지식/)
- [1.1 운영체제](docs/1-cs-전공-지식/1-1-운영체제/) - 프로세스/스레드, 동시성, 메모리 관리, 스케줄링
- [1.2 네트워크](docs/1-cs-전공-지식/1-2-네트워크/) - OSI/TCP-IP, TCP/UDP, HTTP, DNS, 보안
- [1.3 데이터베이스](docs/1-cs-전공-지식/1-3-데이터베이스/) - ACID/CAP, 인덱스, 트랜잭션, NoSQL
- [1.4 자료구조](docs/1-cs-전공-지식/1-4-자료구조/) - 배열, 연결리스트, 트리, 그래프, 해시
- [1.5 알고리즘](docs/1-cs-전공-지식/1-5-알고리즘/) - 정렬, 탐색, 그래프, 동적계획법, 그리디
- [1.6 분산 시스템](docs/1-cs-전공-지식/1-6-분산-시스템/) - 일관성, 합의 알고리즘, 분산 트랜잭션
- [1.7 시스템 디자인](docs/1-cs-전공-지식/1-7-시스템-디자인-and-성능-최적화/) - 캐싱, 로드밸런싱, 확장성, 성능 최적화

### 🧠 [2. 기초 딥러닝](docs/2-기초-딥러닝/)
- [2.1 신경망 기초](docs/2-기초-딥러닝/2-1-신경망-기초/) - 퍼셉트론, 활성화 함수, 최적화
- [2.2 CNN](docs/2-기초-딥러닝/2-2-합성곱-신경망-cnn/) - 합성곱, 풀링, ResNet, EfficientNet
- [2.3 RNN/LSTM](docs/2-기초-딥러닝/2-3-순환-and-시퀀스-모델-선택/) - 순환 신경망, 시퀀스 모델링
- [2.4 트랜스포머](docs/2-기초-딥러닝/2-4-트랜스포머-and-어텐션/) - 어텐션, 인코더-디코더
- [2.5 학습 최적화](docs/2-기초-딥러닝/2-5-학습-팁-and-엔지니어링/) - 정규화, 하이퍼파라미터 튜닝
- [2.6 생성 모델](docs/2-기초-딥러닝/2-6-생성-모델/) - VAE, GAN, Diffusion
- [2.7 그래프 신경망](docs/2-기초-딥러닝/2-7-그래프-신경망-gnn/) - GNN 기초와 응용
- [2.8 강화학습](docs/2-기초-딥러닝/2-8-강화-학습-rl/) - Q-Learning, Policy Gradient
- [2.9 모델 해석성](docs/2-기초-딥러닝/2-9-모델-해석가능성-and-책임/) - XAI, 편향성 분석

### 👁️ [3. 컴퓨터 비전](docs/3-컴퓨터-비전/)
- [3.1 핵심 태스크](docs/3-컴퓨터-비전/3-1-핵심-태스크/) - 분류, 객체 탐지, 세그멘테이션
- [3.2 학습 패러다임](docs/3-컴퓨터-비전/3-2-학습-패러다임/) - 전이학습, 자기지도학습
- [3.3 대표 모델](docs/3-컴퓨터-비전/3-3-대표-모델/) - YOLO, R-CNN, ViT
- [3.4 멀티모달](docs/3-컴퓨터-비전/3-4-비디오-3d-멀티모달/) - 비디오, 3D, 텍스트-이미지
- [3.5 평가 지표](docs/3-컴퓨터-비전/3-5-평가-지표/) - mAP, IoU, F1-Score
- [3.6 배포 최적화](docs/3-컴퓨터-비전/3-6-배포-and-최적화/) - 경량화, 양자화, 추론 가속

### 🤖 [4. 대규모 언어 모델 (LLM)](docs/4-대규모-언어-모델-llm/)
- [4.1 트랜스포머 심화](docs/4-대규모-언어-모델-llm/4-1-트랜스포머-심화/) - Scaled Dot-Product Attention
- [4.2 사전 학습](docs/4-대규모-언어-모델-llm/4-2-사전-학습-목표/) - Masked LM, Next Token Prediction
- [4.3 파인튜닝](docs/4-대규모-언어-모델-llm/4-3-파인튜닝-and-정렬/) - LoRA, QLoRA, PEFT
- [4.4 응용 태스크](docs/4-대규모-언어-모델-llm/4-4-응용-and-태스크/) - 요약, 번역, QA, 코드 생성
- [4.5 대표 모델](docs/4-대규모-언어-모델-llm/4-5-대표-모델-and-벤치마크/) - GPT, BERT, T5, PaLM
- [4.6 서빙/배포](docs/4-대규모-언어-모델-llm/4-6-서빙-and-배포/) - vLLM, TensorRT-LLM, 배치 처리
- [4.7 Tool Calling](docs/4-대규모-언어-모델-llm/4-7-tool-or-function-calling-and-json-schema/) - Function Calling, JSON Schema
- [4.8 평가/모니터링](docs/4-대규모-언어-모델-llm/4-8-평가-프레임워크-and-모니터링/) - BLEU, ROUGE, 환각 탐지
- [4.9 컨텍스트 확장](docs/4-대규모-언어-모델-llm/4-9-context-확장-and-최적화/) - RoPE, ALiBi, Longformer
- [4.10 비용 최적화](docs/4-대규모-언어-모델-llm/4-10-비용-최적화-전략/) - 모델 압축, 캐싱 전략

<details>
<summary><strong>📋 전체 목차 펼쳐보기</strong></summary>

### 🎯 [5. Agentic AI](docs/5-agentic-ai/)
- [5.1 핵심 개념](docs/5-agentic-ai/5-1-핵심-개념/) - Agent, Tool, Planning, Memory
- [5.2 프레임워크](docs/5-agentic-ai/5-2-프레임워크-라이브러리/) - LangChain, AutoGPT, CrewAI
- [5.3 아키텍처 패턴](docs/5-agentic-ai/5-3-아키텍처-패턴/) - ReAct, Chain-of-Thought, Tree-of-Thoughts
- [5.4 RAG 시스템](docs/5-agentic-ai/5-4-retrieval-augmented-generation-rag/) - 검색 증강 생성, Vector DB, 하이브리드 검색
- [5.5 프로토콜](docs/5-agentic-ai/5-5-프로토콜-and-표준/) - OpenAI Function Calling, Anthropic MCP, A2A, ACP
- [5.6 평가/모니터링](docs/5-agentic-ai/5-6-평가-and-모니터링/) - Agent 성능 측정, 디버깅
- [5.7 엔지니어링](docs/5-agentic-ai/5-7-엔지니어링-플레이북/) - Prompt Engineering, 에러 처리
- [5.8 워크플로우](docs/5-agentic-ai/5-8-오케스트레이션-and-워크플로우-관리/) - Multi-Agent 시스템, 오케스트레이션
- [5.9 보안/격리](docs/5-agentic-ai/5-9-보안-and-격리/) - 샌드박스, 권한 관리

### ⚙️ [6. MLOps & 배포](docs/6-mlops-and-배포/)
- [6.1 ML 파이프라인](docs/6-mlops-and-배포/6-1-ml-파이프라인-전반/) - 데이터 전처리, 모델 학습, 배포
- [6.2 CI/CD for ML](docs/6-mlops-and-배포/6-2-ci-or-cd-for-ml/) - 모델 버전 관리, 자동화된 테스트
- [6.3 Feature Store](docs/6-mlops-and-배포/6-3-feature-store/) - 특성 관리, 데이터 일관성
- [6.4 모니터링](docs/6-mlops-and-배포/6-4-모니터링-and-드리프트/) - 드리프트 탐지, 성능 모니터링
- [6.5 파이프라인 도구](docs/6-mlops-and-배포/6-5-파이프라인-도구/) - Kubeflow, MLflow, DVC

### 🔧 [7. 데이터 엔지니어링](docs/7-데이터-엔지니어링-and-파이프라인/)
- [7.1 배치 vs 스트리밍](docs/7-데이터-엔지니어링-and-파이프라인/7-1-배치-vs-스트리밍/) - 실시간 처리, 마이크로배치
- [7.2 CDC & 데이터 품질](docs/7-데이터-엔지니어링-and-파이프라인/7-2-cdc-and-데이터-품질/) - 변경 데이터 캡처, 데이터 검증
- [7.3 오케스트레이션](docs/7-데이터-엔지니어링-and-파이프라인/7-3-오케스트레이션/) - Airflow, Prefect, Dagster
- [7.4 데이터 웨어하우스](docs/7-데이터-엔지니어링-and-파이프라인/7-4-데이터-웨어하우스-and-레이크/) - Snowflake, BigQuery, 데이터 레이크

### 🛡️ [8. 책임 AI & 윤리](docs/8-책임-ai-윤리-보안/)
- [8.1 편향성 & 공정성](docs/8-책임-ai-윤리-보안/8-1-bias-and-fairness/) - 알고리즘 편향, 공정성 지표
- [8.2 개인정보 보호](docs/8-책임-ai-윤리-보안/8-2-개인정보-and-프라이버시/) - 차분 프라이버시, 연합 학습
- [8.3 규제 & 거버넌스](docs/8-책임-ai-윤리-보안/8-3-규제-and-거버넌스/) - GDPR, AI Act, 모델 거버넌스
- [8.4 모델 해석 & 감사](docs/8-책임-ai-윤리-보안/8-4-모델-해석-and-감사/) - LIME, SHAP, 투명성
- [8.5 공격 & 방어](docs/8-책임-ai-윤리-보안/8-5-공격-and-방어/) - 적대적 공격, 모델 보안

### 📱 [9. 엣지 AI & 온디바이스 ML](docs/9-엣지-ai-and-온디바이스-ml/)
- [9.1 TinyML 개요](docs/9-엣지-ai-and-온디바이스-ml/9-1-tinyml-개요/) - 경량화 기법, 모델 압축
- [9.2 온디바이스 최적화](docs/9-엣지-ai-and-온디바이스-ml/9-2-온디바이스-모델-최적화/) - 양자화, 프루닝, 지식 증류
- [9.3 엣지 런타임](docs/9-엣지-ai-and-온디바이스-ml/9-3-edge-runtime/) - TensorFlow Lite, ONNX Runtime
- [9.4 하드웨어 가속기](docs/9-엣지-ai-and-온디바이스-ml/9-4-하드웨어-가속기/) - NPU, TPU, 모바일 GPU

</details>

---

### 📚 [부록 자료](docs/부록/)
- [면접 질문 은행](docs/부록/interview-question-bank.md) - 실전 면접 대비 핵심 질문 모음
- [용어집](docs/부록/glossary.md) - AI/ML 주요 용어 정리
- [공식 및 치트시트](docs/부록/cheatsheets-and-formulas.md) - 핵심 공식과 참고 자료
- [추천 리소스](docs/부록/recommended-resources.md) - 학습에 도움되는 자료 모음

---

## 🚀 시작하기

### 1. 클론 및 설치

```bash
git clone https://github.com/harimkang/ai-engineer-guide.git
cd ai-engineer-guide
```

### 2. 문서 구조

```
docs/
├── index.md                    # 전체 목차
├── 1-cs-전공-지식/             # CS 기초
├── 2-기초-딥러닝/              # 딥러닝 기초
├── 3-컴퓨터-비전/              # 컴퓨터 비전
├── 4-대규모-언어-모델-llm/      # LLM
├── 5-agentic-ai/              # Agentic AI
├── 6-mlops-and-배포/          # MLOps
├── 7-데이터-엔지니어링-and-파이프라인/ # 데이터 엔지니어링
├── 8-책임-ai-윤리-보안/        # AI 윤리
├── 9-엣지-ai-and-온디바이스-ml/ # 엣지 AI
├── 부록/                      # 참고 자료
└── assets/                   # 이미지, 파일 리소스
```

### 3. 학습 방법

1. **순차적 학습**: 1번부터 9번까지 순서대로 학습하는 것을 권장
2. **선택적 학습**: 관심 분야나 부족한 부분만 골라서 학습
3. **복습**: 면접 전 [docs/부록/interview-question-bank.md](docs/부록/interview-question-bank.md)로 핵심 질문 복습

---

## 📝 문서 작성 가이드라인

### 기여하고 싶으신가요?

이 프로젝트는 오픈소스이며, 여러분의 기여를 환영합니다! 문서 작성 시 다음 가이드라인을 참고해주세요: [DOCS-GUIDE.md](DOCS-GUIDE.md)

- 🎯 **명확하고 간결한 설명**: 복잡한 개념도 쉽게 이해할 수 있도록
- 📊 **시각적 자료 활용**: 다이어그램, 표, 이미지로 직관적 설명
- 🔗 **실제 활용 예시**: 이론과 실무를 연결하는 구체적인 예시

### 기여 방법

1. 레포지토리 Fork
2. feature 브랜치 만들기 (`git checkout -b feature/add-1`)
3. Commit 하기 (`git commit -m 'Add some amazing feature'`)
4. Push 하기 (`git push origin feature/amazing-feature`)
5. PR (Pull Request) 만들기

---

## 🎯 대상 독자

- 🎓 **AI 개발자 취업 준비생**: 기술 면접 대비가 필요한 분
- 💼 **현직 개발자**: AI 분야로 전환을 준비하는 분
- 📚 **학생**: AI/ML 관련 전공 학습을 보완하고 싶은 분
- 🔄 **기술 리뷰**: 기존 지식을 체계적으로 정리하고 싶은 분

---

**⭐ 이 프로젝트가 도움이 되셨다면 Star를 눌러주세요!**
