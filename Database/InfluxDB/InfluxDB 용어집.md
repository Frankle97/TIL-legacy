# InfluxDB 용어집

InfluxDB에서 자주 사용되는 용어를 정리한다.

### Aggregation
InfluxQL(InfluxDB Query Language)의 함수 중 하나로, 포인트 셋 전체에 걸쳐 집계된 값을 반환.


### Batch
데이터 포인트들의 집합. 
포인트들의 배치는 write endpoints에 대한 단일 HTTP 요청에 의해 데이터베이스에 제출된다.
InfluxDB API를 사용하여 데이터를 저장할 때 HTTP 오버헤드를 줄이면 성능이 대폭 향상될 수 있다.
배치의 크기가 더 작거나 커도 InfluxDB는 잘 동작하겠지만, 배치의 크기는 5000~10000 포인트로 권장하고 있다.

### Bucket
버킷은 시계열 데이터가 저장되는 위치이다.

### [Continuous Query (CQ)](https://docs.influxdata.com/influxdb/v1.8/query_language/continuous_queries/)
CQ는 데이터베이스 내에서 자동적이며 주기적으로 실행되는 InfluxQL을 의미한다.
CQ는 SELECT 절이 필요하며 GROUP BY time() 절을 포함해야만 한다.

### Duration
InfluxDB가 데이터를 저장하는 기간 결정하는 Retention Policy의 속성. 기간 보다 오래 지난 데이터는 자동적으로 삭제된다.

[Database Management](https://docs.influxdata.com/influxdb/v1.8/query_language/manage-database/) 에서 저장 기간이 어떻게 설정되는지 확인이 가능하다

### Field
InfluxDB 데이터 구조인 Key-Value 포맷을 Field라고 한다.
데이터 구조에 필드는 필수적으로 포함되어야하지만, Tag와 다르게 인덱싱 기능을 지원하지는 않는다.

### Field Key
필드를 구성하는 Key-Value 형식의 키를 나타내는 부분이다.
필드 키는 문자열이다.

### Field Value
필드를 구성하는 Key-Value 의 값을 나타내는 부분이다.
필드 값은 실제 데이터로써 문자열, 소수, 정수, 불린의 값을 가질 수 있다.
필드 값은 항상 타임스탬프와 관련이 있다.
필드 값은 인덱싱을 지원하지 않으며, 필드에 대한 쿼리는 지정된 시간 범위와 일치하는 모든 포인트에 대해 검색하므로 태그처럼 빠르지 않다.

### Field Set
한 포인트의 필드 Key-Value 쌍의 집합이다.

### Measurement
InfluxDB의 데이터 구조의 한 부분이며 문자열로 이루어진다.

### MetaStore
시스템 상태를 담는 내부 정보이다. 유저 정보, 데이터베이스, 리텐션 폴리시, 샤드 메타 데이터, CQ 등의 정보를 포함한다.

### Node
독립된 Influxd 프로세스를 말한다.

### now()
현재 로컬의 nanosecond timestamp 를 반환한다.

### Point
포인트는 단일 데이터 레코드를 의미한다. RDBMS 테이블의 한 행이라고 생각하면 된다.

각 포인트는 measurement, tag set, field set, timestamp 를 가진다.
이것은 series와 timestamp에 의해 유니크하게 식별된다.
하나의 series엔 timestamp가 중복될 수 없다.

### [Retention Policy](https://docs.influxdata.com/influxdb/v1.8/query_language/manage-database/)
InfluxDB의 데이터 보존 기간, 클러스터 환경에서 데이터 복사량, 샤드 그룹들을 커버하는 기간을 의미한다.
데이터 베이스당 유일하며, measurement, tag-set과 함께 시리즈를 정의한다.
데이터베이스를 생성할 때, InfluxDB는 autogen이라는 Retention Policy를 생성한다.
autogen이라는 Policy는 보존기간이 무한이며, replication factor는 1일, 샤드 그룹 보존 기간은 7일이다.

### Schema
InfluxDB에서 데이터가 어떻게 구조화되어 있는지 설명한다.
InfluxDB의 주요 스키마는 데이터베이스, RP, 시리즈, 메저먼트, 태그 키-값을 의미한다.

### Series
measurement, tag set, field key에서 공유하는 논리적으로 그룹화된 데이터를 의미한다.

### Series Key
메저먼트, 태그 셋, 필드키에 의해 특정 시리즈를 식별하는 키이다.

### Tag
InfluxDB의 데이터 구조 중 메타데이터를 기록하는 키-값 쌍이다.
Tag는 인덱싱된다.

### Timestamp
모든 포인트에서 데이터와 연관된 시간이다.
InfluxDB의 모든 시간은 UTC이다. 데이터 쓰기 시, 시간을 지정하는 방법에 대해서는 [Write Syntax](https://docs.influxdata.com/influxdb/v1.8/write_protocols/line_protocol_reference/) 를, 데이터 탐색 시 시간을 지정하는 방법에 대해서는 [Data Exploration](https://docs.influxdata.com/influxdb/v1.8/query_language/explore-data/) 를 참고하자.  


---
https://gurumee92.tistory.com/193


