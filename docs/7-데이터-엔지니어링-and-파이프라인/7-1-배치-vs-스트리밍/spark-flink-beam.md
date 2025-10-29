---
title: '분산 데이터 처리 엔진: Spark, Flink, Beam 비교'
date: '2025-07-07'
tags: [Data Engineering, Distributed Computing, Spark, Flink, Beam]
difficulty: hard
---

# 분산 데이터 처리 엔진: Spark, Flink, Beam 비교

## 1. 핵심 개념 (Core Concept)

Apache Spark, Apache Flink, Apache Beam은 대용량 데이터를 여러 노드에 분산하여 병렬로 처리하는 핵심 데이터 엔지니어링 도구임. **Spark**는 범용적인 인메모리 처리로 배치와 스트리밍을 모두 지원하는 가장 대중적인 엔진이며, **Flink**는 낮은 지연 시간의 진정한 스트림 처리에 특화되어 있음. **Beam**은 특정 엔진이 아닌, 다양한 실행 엔진(Runner)에서 코드를 실행할 수 있도록 하는 통합 프로그래밍 모델로, 코드의 이식성을 제공함.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 처리 모델의 차이

- **Apache Spark**: **마이크로 배치(Micro-batch)** 스트리밍. 실시간으로 들어오는 데이터를 아주 작은 배치(수백 ms ~ 수초)로 묶어 처리. 이는 높은 처리량(Throughput)을 유지하면서 거의 실시간에 가까운 결과를 제공. 범용성과 안정성이 뛰어남.

- **Apache Flink**: **네이티브 스트림(Native Stream)** 처리. 데이터를 들어오는 즉시 하나씩(event-at-a-time) 처리. 이를 통해 수 밀리초(ms) 수준의 매우 낮은 지연 시간(Low Latency)을 달성할 수 있으며, 정교한 시간 기반 윈도우(Window) 연산과 상태(State) 관리에 매우 강력함.

- **Apache Beam**: **통합 모델(Unified Model)**. 배치와 스트리밍을 모두 아우르는 단일 프로그래밍 모델을 제공. 개발자는 Beam의 API로 파이프라인을 작성하고, 이를 실행할 때 Spark, Flink, Google Cloud Dataflow 등 원하는 실행 엔진(Runner)을 선택할 수 있음. 특정 엔진에 대한 종속성을 줄이는 데 목적이 있음.

### 2.2 비교 분석 (2025년 기준)

| 구분                   | Apache Spark                                                                          | Apache Flink                                                                  | Apache Beam                                                                             |
| :--------------------- | :------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------- |
| **주요 처리 방식**     | 인메모리 마이크로 배치                                                                | 네이티브 스트리밍                                                             | 통합 프로그래밍 모델                                                                    |
| **스트리밍 지연 시간** | 낮음 (수백 ms ~ 초)                                                                   | 매우 낮음 (수 ms)                                                             | 실행 엔진에 따라 다름                                                                   |
| **상태 관리**          | Checkpointing 기반                                                                    | Checkpointing 및 Savepoints, 더 정교함                                        | 실행 엔진에 위임                                                                        |
| **핵심 장점**          | - 방대한 생태계 (SQL, MLlib, GraphX)<br>- 높은 범용성 및 안정성<br>- 가장 큰 커뮤니티 | - 매우 낮은 지연 시간<br>- 정확한 이벤트 시간 처리<br>- 복잡 이벤트 처리(CEP) | - 코드 이식성 (Runner 교체 가능)<br>- 특정 엔진 종속성 제거<br>- 배치/스트리밍 통합 API |
| **주요 단점**          | Flink 대비 높은 스트리밍 지연 시간                                                    | Spark 대비 작은 커뮤니티 및 생태계                                            | - 추상화로 인한 성능 손실 가능성<br>- 엔진별 고급 기능 사용 제한                        |
| **적합한 워크로드**    | ETL, 배치 분석, ML, 준실시간 스트리밍                                                 | 실시간 이상 탐지, 실시간 추천, 금융 거래 분석                                 | 멀티 클라우드/하이브리드 환경, 기술 종속성을 피하고 싶은 경우                           |

______________________________________________________________________

## 3. 예시 (Example)

### 사용 사례

- **Spark**: 소셜 미디어 회사가 매일 수십억 개의 로그를 분석하여 사용자 행동 리포트를 생성하고, 이 데이터를 기반으로 MLlib을 사용하여 추천 모델을 주기적으로 학습시키는 경우.
- **Flink**: 신용카드 회사가 매초 수만 건의 거래 데이터를 실시간으로 처리하여, 사기 패턴에 해당하는 거래가 발생하는 즉시(수십 ms 내에) 탐지하고 결제를 차단하는 시스템을 구축하는 경우.
- **Beam**: 한 기업이 온프레미스 환경에서는 Spark를 사용하여 배치 파이프라인을 실행하고, 클라우드(GCP)에서는 Dataflow를 사용하여 스트리밍 파이프라인을 실행해야 할 때, Beam을 사용하여 한 번만 코드를 작성하고 각 환경에 맞는 Runner를 지정하여 파이프라인을 실행하는 경우.

### 코드 스타일 비교 (Word Count)

**Spark (PySpark):**

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("WordCount").getOrCreate()
lines = spark.read.text("input.txt").rdd.map(lambda r: r[0])
counts = lines.flatMap(lambda x: x.split(' ')) \
              .map(lambda x: (x, 1)) \
              .reduceByKey(lambda a, b: a + b)
counts.saveAsTextFile("output")
```

**Flink (PyFlink):**

```python
from pyflink.datastream import StreamExecutionEnvironment

env = StreamExecutionEnvironment.get_execution_environment()
source = env.from_collection(["hello world", "hello flink"])
counts = source.flat_map(lambda x: x.split(' ')) \
               .map(lambda x: (x, 1)) \
               .key_by(lambda x: x[0]) \
               .reduce(lambda a, b: (a[0], a[1] + b[1]))
counts.print()
env.execute("WordCount")
```

**Beam (Python SDK):**

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
    counts = (
        pipeline
        | 'Read' >> beam.io.ReadFromText('input.txt')
        | 'Split' >> beam.FlatMap(lambda x: x.split(' '))
        | 'PairWithOne' >> beam.Map(lambda x: (x, 1))
        | 'GroupAndSum' >> beam.CombinePerKey(sum)
        | 'Write' >> beam.io.WriteToText('output')
    )
```

*위 Beam 코드는 `--runner=SparkRunner` 또는 `--runner=FlinkRunner` 옵션을 통해 다른 엔진에서 실행될 수 있음.*

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. Spark와 Flink의 스트리밍 처리 방식의 근본적인 차이점은 무엇이며, 이로 인해 어떤 사용 사례의 차이가 발생하나요?**
  - **A.** 근본적인 차이는 **처리 단위**에 있습니다. Spark는 **마이크로 배치** 방식으로, 데이터를 아주 작은 묶음으로 나누어 처리합니다. 이는 높은 처리량을 보장하지만 약간의 지연 시간이 발생합니다. 반면, Flink는 **네이티브 스트리밍** 방식으로, 데이터를 이벤트 하나하나 발생 즉시 처리합니다. 이로 인해 Flink는 수 밀리초 수준의 매우 낮은 지연 시간을 달성할 수 있습니다. 따라서, 일반적인 ETL이나 준실시간 분석에는 Spark가 적합하지만, 금융 사기 탐지나 실시간 가격 책정처럼 즉각적인 반응이 필수적인 시스템에는 Flink가 훨씬 더 유리합니다.
- **Q. Apache Beam을 사용하면 어떤 이점이 있으며, 어떤 경우에 Beam을 선택하는 것이 좋을까요?**
  - **A.** Beam의 가장 큰 이점은 **코드의 이식성**과 **미래 대비**입니다. Beam의 통합 프로그래밍 모델로 코드를 한 번만 작성하면, Spark, Flink, Google Cloud Dataflow 등 다양한 실행 엔진에서 동일한 코드를 실행할 수 있습니다. 이는 특정 기술이나 클라우드 벤더에 대한 종속을 피하게 해줍니다. 따라서, 하이브리드 클라우드 전략을 사용하거나, 현재는 온프레미스에서 Spark를 쓰지만 미래에 클라우드의 Flink 기반 서비스로 이전할 가능성을 열어두고 싶을 때 Beam을 선택하는 것이 매우 현명한 결정입니다.
- **Q. Spark가 Flink에 비해 갖는 가장 큰 경쟁력은 무엇이라고 생각하시나요?**
  - **A.** Spark의 가장 큰 경쟁력은 **성숙도와 방대한 생태계**입니다. Spark는 오랫동안 데이터 처리 시장의 표준으로 자리 잡아왔기 때문에 매우 안정적이고, 수많은 기업에서 검증되었습니다. 또한, SQL(Spark SQL), 머신러닝(MLlib), 그래프 처리(GraphX) 등 데이터 분석에 필요한 거의 모든 기능을 하나의 프레임워크 안에서 해결할 수 있는 강력한 생태계를 갖추고 있습니다. 이로 인해 개발자는 다양한 요구사항을 빠르고 쉽게 해결할 수 있으며, 관련 자료나 커뮤니티 지원을 얻기도 훨씬 용이합니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Apache Spark Official Website](https://spark.apache.org/)
- [Apache Flink Official Website](https://flink.apache.org/)
- [Apache Beam Official Website](https://beam.apache.org/)
- [Spark vs. Flink: A Detailed Comparison (Databricks Blog)](https://www.databricks.com/blog/2023/08/21/spark-vs-flink-detailed-comparison.html)
