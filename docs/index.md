# AI 엔지니어가 알아야할 기술 문서 – 목차 (KOR)

---

## 1. CS 전공 지식

### 1.1 운영체제

* 프로세스 vs 스레드 (생명주기, 컨텍스트 스위칭)
* 동시성 & 동기화 (뮤텍스, 세마포어, 모니터, Python GIL)
* 메모리 관리 (가상 메모리, 페이징, 세그멘테이션, 가비지 컬렉션)
* 스케줄링 알고리즘 (FCFS, SJF, 라운드로빈, MLFQ)
* 파일 시스템 & I/O (버퍼 캐시, 저널링)
* 시스템 콜 & 커널/유저 모드
* 교착 상태 (Coffman 조건, 예방·회피·복구)

### 1.2 네트워크

* OSI 7계층 vs TCP/IP 4계층
* TCP vs UDP, 3-Way Handshake, 혼잡 제어
* HTTP/1.1, HTTP/2, HTTP/3 & gRPC
* DNS, 로드 밸런싱, CDN 기본
* TLS/SSL, 인증서 & HTTPS
* 소켓 프로그래밍 (Python `socket` 예시)
* REST vs GraphQL vs gRPC API 설계

### 1.3 데이터베이스

* 관계형 vs NoSQL (문서, 키-값, 그래프)
* ACID, CAP 정리 & BASE
* 인덱스 구조 (B-Tree, 해시 인덱스, 역색인)
* 조인 & 쿼리 최적화 (`EXPLAIN` 플랜)
* 트랜잭션 격리 수준 & 잠금
* 복제, 샤딩, 파티셔닝
* Python ORM (SQLAlchemy) 사용 예시

### 1.4 자료구조

* 배열 & Python 리스트 (append, insert, 재할당)
* 연결 리스트, 스택, 큐, 데크
* 트리 (BST, AVL, Red-Black, B-Tree)
* 힙 & 우선순위 큐 (Python `heapq`)
* 해시 테이블 & Python 딕셔너리 내부 구조
* 그래프 표현 (인접 리스트/행렬)
* 트라이, 유니온-파인드

### 1.5 알고리즘

* 정렬 (퀵, 병합, 힙, TimSort)
* 탐색 (이진 탐색, 해시 조회)
* 그래프 알고리즘 (BFS, DFS, 다익스트라, A\*, 위상 정렬)
* 동적 계획법 (배낭 문제, LIS)
* 그리디 (활동 선택, 허프만 코딩)
* 분할 정복 (스트라센, FFT)
* 복잡도 분석 & Big-O 치트시트
* NP-완전 & 근사 알고리즘

### 1.6 분산 시스템

* 일관성 모델 (강·약·최종)
* 리더 선출 & 합의 (Paxos, Raft)
* 분산 트랜잭션 (2PC, 3PC, Sagas)
* 멀티-리전 & 장애 허용 (Replication Factor, Quorum)
* 이벤트 순서 & 시간 (Logical/Vector Clock)

### 1.7 시스템 디자인 & 성능 최적화

* 캐시 계층 & CDN 전략
* 큐잉 시스템 & 백프레셔
* Autoscaling, GPU 오케스트레이션(K8s, KEDA)
* GPU Job Scheduler 디자인 (NVIDIA K8s Operator)
* 데이터 중심 LLM 서비스 디자인 (Vector DB + LLM 캐시)
* 관찰 가능성 (로그, 메트릭, 트레이싱)

---

## 2. 기초 딥러닝

### 2.1 신경망 기초

* 퍼셉트론 & MLP
* 활성화 함수 (ReLU, GELU, SiLU)
* 손실 함수 (교차 엔트로피, MSE, Focal Loss)
* 순전파 & 역전파 수식
* 최적화 알고리즘 (SGD, Momentum, Adam, AdamW)
* 정규화 (L1/L2, 드롭아웃, BatchNorm, LayerNorm)
* 가중치 초기화 & 초기화 문제

### 2.2 합성곱 신경망 (CNN)

* 합성곱 연산 (커널, 스트라이드, 패딩, 딜레이션)
* 풀링 층 & 글로벌 평균 풀링
* 초기 CNN 아키텍처: LeNet, AlexNet, VGG
* Inception & GoogLeNet (1×1 컨볼루션)
* ResNet & 변형 (ResNeXt, WideResNet, EfficientNet-V2)
* Depthwise-Separable Conv (MobileNet, Xception)

### 2.3 순환 & 시퀀스 모델 (선택)

* RNN, LSTM, GRU 한계와 개선
* Seq2Seq & 어텐션 도입

### 2.4 트랜스포머 & 어텐션

* Self-Attention 메커니즘 & Scaled Dot-Product
* 멀티-헤드 어텐션 & 위치 인코딩
* 인코더-디코더 vs 디코더-온리
* 효율적 트랜스포머 (Linformer, Performer, Flash-Attention)

### 2.5 학습 팁 & 엔지니어링

* 학습률 스케줄링 (워밍업, 코사인, One-Cycle)
* 혼합 정밀도 & 그라디언트 체크포인팅
* 전이 학습 & 파인튜닝
* 하이퍼파라미터 탐색 (Optuna 예시)
* PyTorch 실전 팁 & 보일러플레이트
* 프롬프트 엔지니어링 원칙 (Zero/One-Shot, Chain-of-Thought 템플릿)

### 2.6 생성 모델

* GAN (DCGAN, StyleGAN, GAN 안정화 기법)
* 확산 모델 (DDPM, Stable Diffusion, SDXL)
* 평가 지표 (FID, IS, CLIP-Score)

### 2.7 그래프 신경망 (GNN)

* GCN, GraphSAGE, GAT
* 그래프 풀링 & 읽기 연산
* 추천 시스템·지식 그래프 적용

### 2.8 강화 학습 (RL)

* MDP & 벨만 방정식
* Q-러닝 & DQN
* 정책 그래디언트 & PPO
* RLHF, DPO 개요

### 2.9 모델 해석가능성 & 책임

* SHAP, LIME, Grad-CAM
* Bias 탐지 & 완화 기법

---

## 3. 컴퓨터 비전

### 3.1 핵심 태스크

* 이미지 분류
* 객체 탐지 (Two-Stage vs One-Stage)
* 시맨틱 세그멘테이션
* 인스턴스·파놉틱 세그멘테이션
* 키포인트 & 포즈 추정
* 이미지 생성 (GAN, 확산 모델)

### 3.2 학습 패러다임

* 지도 학습
* 준지도 학습 (Pseudo-Label, FixMatch, MeanTeacher)
* 자기지도·비지도 (SimCLR, MoCo, BYOL, MAE, DINOv2)

### 3.3 대표 모델

* CNN 계열: GoogleNet, ResNet, DenseNet
* 경량 모델: MobileNet (V1/V2/V3), ShuffleNet, EfficientNet
* 비전 트랜스포머: ViT, DeiT, Swin, ConvNeXt
* 탐지 모델: Faster R-CNN, SSD, RetinaNet, YOLO (v8)
* 세그멘테이션: FCN, U-Net, DeepLab, Mask R-CNN

### 3.4 비디오·3D·멀티모달

* 비디오 인식 (SlowFast, I3D, VideoMAE)
* 3D 비전 & NeRF
* 멀티모달 모델 (CLIP, FLAVA, Flamingo)
* OCR·Document AI (LayoutLM, Donut)

### 3.5 평가 지표

* 정확도, Top-K Error
* IoU & mAP (Pascal / COCO)
* Dice, Jaccard, F1

### 3.6 배포 & 최적화

* 모델 압축 (프루닝, 양자화, 지식 증류)
* 실시간·경량 추론 (TensorRT INT4, NPU)
* 하드웨어 가속 (ONNX, OpenVINO)
* 엣지·모바일 배포 (TFLite, CoreML)

---

## 4. 대규모 언어 모델 (LLM)

### 4.1 트랜스포머 심화

* 어텐션 수식 상세
* Causal Masking & 위치 인코딩 재조명
* 스케일링 법칙 & 파라미터/연산 트레이드오프
* 메모리·속도 최적화 (KV-Cache, Flash-Attention-2)

### 4.2 사전 학습 목표

* Masked LM (BERT 계열)
* Causal LM (GPT 계열)
* 시퀀스-투-시퀀스 & 번역 (T5, mT5)
* 멀티모달 사전학습 (CLIP, Flamingo, Gemini)

### 4.3 파인튜닝 & 정렬

* 감독식 파인튜닝 (SFT)
* 파라미터 효율 튜닝 (LoRA, Adapter, Prefix)
* RLHF & DPO
* 안전성·정렬 기법

### 4.4 응용 & 태스크

* 텍스트 생성·요약
* 질문 응답 & Retrieval-QA
* 코드 생성 & 보완
* 임베딩 & 의미 검색

### 4.5 대표 모델 & 벤치마크

* GPT-3/4/4-o, GPT-4-o-mini
* PaLM-2, Gemini, Claude 3
* 오픈 모델: Llama-3, Mistral, Mixtral, Falcon
* 평가 스위트: Perplexity, BLEU/ROUGE, MMLU, HELM, MT-Bench

### 4.6 서빙 & 배포

* 토크나이저 (BPE, SentencePiece)
* 양자화 (8-bit, 4-bit, GGUF, AWQ)
* 추론 엔진 (vLLM, TensorRT-LLM, TGI)
* 분산 서빙 & 샤딩

### 4.7 Tool/Function Calling & JSON Schema

* OpenAI Function Calling
* Google Vertex AI Tool Calling
* Tool 호출 표준화 & 안전 가드

### 4.8 평가 프레임워크 & 모니터링

* OpenAI Evals, RAGAS, TruLens
* Continuous Evaluation & Canary

### 4.9 Context 확장 & 최적화

* Long Context (LongRope, FlashAttention-3)
* MoE Router & Sparse Mixture

### 4.10 비용 최적화 전략

* Spot Instances & Reserved Capacity
* KV-Cache 공유 & 메모리 재사용
* 서버리스 GPU 인프라 (Lambda, Run\:AI)

---

## 5. Agentic AI

### 5.1 Agentic AI 시스템 설계

* AI 에이전트 정의 (LLM-based, Autonomous, Goal-driven)
* RPA 루프(Reasoning/Planning/Acting), Reflection/Self-critique/Re-asking
* 구성 요소: Model, Tools, Orchestration, Memory, Context Manager, Evaluator
* 에이전트 간 상호작용: A2A, ACP
* Ecosystem: OpenDevin, AutoGen, CrewAI, LangGraph, LlamaIndex, MemGPT, AirOps

### 5.2 메모리 & 컨텍스트 관리

* 단기/장기/일화 메모리, RAG 메모리
* 컨텍스트 관리: Sliding window, Summarization, Embedding clustering, Attention window 최적화
* 하이브리드 검색(BM25+Vector)·임계값 튜닝, Contextual grounding & relevance feedback
* 구현: Vector DB(FAISS/Milvus/Chroma), Eviction/Summarization, Hierarchical Memory Graph

### 5.3 핵심 추론 패턴

* Zero-shot / Few-shot
* CoT, ReAct, ToT, GoT, Reflexion

### 5.4 Retrieval-Augmented Generation (RAG)

* 파이프라인: Retriever, Re-Ranker, Generator
* 고급·Agentic RAG: 쿼리 변환(재작성/분해), Multi-step Retrieval, Tool 기반 보강(SQL/웹)
* 기반 기술: 임베딩, 벡터 데이터베이스

### 5.5 프롬프트 엔지니어링 & 평가

* 설계·최적화: A/B, Grid Search, Prompt Tuning vs Instruction Tuning, Human/Synthetic Feedback
* Function/Tool calling 프롬프트 스키마(→ 5.9 툴 스키마)
* 자동 평가 파이프라인(LLM-as-a-Judge), BLEU/ROUGE/정확도/추론 깊이
* 환각 탐지·일관성 검사, 지연/비용 평가(→ 5.6/5.8), 프롬프트 회귀 테스트

### 5.6 AgentOps: 운영 & 자동화

* 라이프사이클: 실행→피드백→개선, 트레이싱·텔레메트리, 리플레이/롤아웃·실패 재현 파이프라인
* 오케스트레이션: 멀티-에이전트(코디네이터/워커), 툴/인텐트 라우팅, LangGraph·CrewAI
* 평가/모니터링: 궤적 기반 평가(T-Eval), 툴 호출 성공률·추론 정확도, 성능 대시보드

### 5.7 LLM 아키텍처 & 최적화

* 트랜스포머/어텐션/포지셔널 인코딩, Decoder-only vs Encoder-Decoder, MoE/희소 어텐션/KV 캐시
* 파인튜닝: PEFT(LoRA/QLoRA), SFT/DPO
* 추론/서빙: 양자화(INT8/INT4), vLLM/Ollama/TGI
* 상세는 4장(LLM) 교차 참조

### 5.8 데이터 & 인프라

* 데이터 엔지니어링: 합성 데이터(Self-play/ask), 증강·필터링, Instruction vs Dialogue
* 인프라: Python 스택(Hydra/Pydantic/Typer/FastAPI), MLOps(Ray/W&B/MLflow), Docker/Supabase/Redis
* CI/CD·자동화: 평가 파이프라인, 재현성(설정 버전/시드), 데이터셋 분할·메트릭 트래킹
* 에이전트 인프라: 프롬프트 캐시, DataPart/TextPart, 컨텍스트 리미터, 응답 평가기, 지연·비용 최적화기

### 5.9 보안 & 프로토콜

* 보안/가드레일: 프롬프트 인젝션, 샌드박스·컨테이너, 최소 권한, 정책 기반 인가(OPA), 감사 로그·레드팀
* 위협모델: PII/시크릿 보호, 네트워크 이그레스·SSRF, 공급망 리스크, 권한 위임 모델
* 표준 프로토콜: A2A, ACP, MCP
* 툴 스키마: JSON-RPC·OpenAPI, 프롬프트 스키마 연계(→ 5.5)

### 5.10 트렌드 & 생태계

* Agents Companion(2024), GenAIOps/PromptOps/AgentOps
* 멀티모달 에이전트, Arena/AgentBench/AlpacaEval, LLM-as-a-Judge
* 주요 프레임워크: OpenDevin, AutoGen, CrewAI, LangGraph, LlamaIndex, MemGPT, AirOps

---

## 6. MLOps & 배포

### 6.1 ML 파이프라인 전반

* 데이터 수집 → 전처리 → 학습 → 배포 → 모니터링
* Reproducibility & 빌드 아티팩트

### 6.2 CI/CD for ML

* GitOps, Pull Request Workflow
* 자동 테스트 (데이터·모델 품질)
* 모델 레지스트리 & 버전 관리 (MLflow, SageMaker)

### 6.3 Feature Store

* 오프라인 vs 온라인 Feature Store
* Feast, Tecton 개요

### 6.4 모니터링 & 드리프트

* 데이터/모델 드리프트 탐지
* 알람 & 롤백 정책

### 6.5 파이프라인 도구

* Kubeflow, Vertex AI Pipelines, SageMaker Pipelines
* Airflow, Dagster, Prefect

---

## 7. 데이터 엔지니어링 & 파이프라인

### 7.1 배치 vs 스트리밍

* Spark, Flink, Beam
* Lambda & Kappa 아키텍처

### 7.2 CDC & 데이터 품질

* Change Data Capture
* 데이터 프로파일링 & 테스트

### 7.3 오케스트레이션

* Airflow DAG 설계 베스트 프랙티스
* Dagster, Prefect 비교

### 7.4 데이터 웨어하우스 & 레이크

* Snowflake, BigQuery, Databricks Lakehouse
* 데이터 레이크 vs 레이크하우스

---

## 8. 책임 AI · 윤리 · 보안

### 8.1 Bias & Fairness

* 데이터 편향 탐지 & 완화
* Equalized Odds, Demographic Parity

### 8.2 개인정보 & 프라이버시

* Differential Privacy, DP-SGD
* 연합 학습 & 안전 데이터 공유

### 8.3 규제 & 거버넌스

* EU AI Act, ISO/IEC 42001
* Model Card, Datasheet

### 8.4 모델 해석 & 감사

* SHAP, LIME, Counterfactuals
* Explainability Dashboard 구축

### 8.5 공격 & 방어

* Adversarial Example, Evasion & Poisoning
* Prompt Injection & Jailbreak 대응

---

## 9. 엣지 AI & 온디바이스 ML

### 9.1 TinyML 개요

* MCU 대상 경량 모델
* 전력·메모리 제약 고려

### 9.2 온디바이스 모델 최적화

* Quantization-Aware Training
* Structured/Sparse Pruning

### 9.3 Edge Runtime

* TFLite Micro, ONNX Runtime Mobile
* MediaPipe, Core ML Tools

### 9.4 하드웨어 가속기

* Edge TPU, NPU, DSP
* SIMD & NEON 활용

---

## 부록

* A. 인터뷰 예상 질문 은행 (섹션별 링크)
* B. 치트시트 & 공식 표
* C. 추천 도서·온라인 강좌
* D. 핵심 용어집 (Glossary)
