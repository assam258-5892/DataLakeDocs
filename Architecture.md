# Data Lake Architecture

Data Lake의 구조은 아래와 같다.

![Data Lake Architecture](images/Data%20Lake%20Architecture.svg)

Data Lake의 각 Layer는 다음의 기능을 수행한다.

|**Layer**|**설명**|
|---|---|
|Primitive Operation Layer|Java로 JDBC와 Hive, HDFS와 직접 접속하여 기본 기능을 처리한다. 호출을 위해서는 연결 정보와 SQL문등의 보다 구체적인 데이터가 필요하다. 대용량의 Bulk 처리에 있어서는 빠른 처리 속도가 필요한 부분도 있다.|
|Single Action Layer|RuleDB로 부터 생성된 설정 파일의 정보로부터 구체적인 연결 정보와 SQL문 등를 생성하여 Primitive Operation Layer를 호출한다. 기능에 따리서는 빠른 처리를 위해서 다중 쓰레드를 사용한 병렬 처리를 수행하는 부분도 있지만, 단일 기능에 대해서만 병렬 처리를 수행한다.|
|Batch & HealthCheck Layer|배치의 수행 전 이중화된 접속 자원 중 사용 가능한 접속 자원에 대한 Health Check를 수행하여 Batch시 사용할 접속 자원을 선택한다. 수집 대상 테이블 혹은 OpenAPI를 병렬 처리로 수집한다. 각 테이블/OpenAPI별 수행 시에 필요한 Single Action들을 순차적으로 수행한다. 수집 로그는 RuleDB에 Log 테이블에 기록된다.|
|Batch & HealthCheck Layer|배치의 수행 전 이중화된 접속 자원 중 사용 가능한 접속 자원에 대한 Health Check를 수행하여 Batch시 사용할 접속 자원을 선택한다. 수집 대상 테이블 혹은 OpenAPI를 병렬 처리로 수집한다. 각 테이블/OpenAPI별 수행 시에 필요한 Single Action들을 순차적으로 수행한다. 수집 로그는 RuleDB에 Log 테이블에 기록된다.|
|Batch & HealthCheck Layer|배치의 수행 전 이중화된 접속 자원 중 사용 가능한 접속 자원에 대한 Health Check를 수행하여 Batch시 사용할 접속 자원을 선택한다. 수집 대상 테이블 혹은 OpenAPI를 병렬 처리로 수집한다. 각 테이블/OpenAPI별 수행 시에 필요한 Single Action들을 순차적으로 수행한다. 수집 로그는 RuleDB에 Log 테이블에 기록된다.|
|Scheduler Layer|매일 정해진 시간에 HealthCheck와 Batch를 수행한다. 실패한 항목이 있는 경우 HealthCheck와 Batch를 재수행하여 실패한 항목만 다시 수집한다.|
|Dashboard Layer|RuleDB에 저장된 배치 수행 Log 테이블을 조회하여, 수집 현황을 Web으로 표현한다.|
