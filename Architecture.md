# Data Lake Architecture

Data Lake의 구조은 아래와 같다.

![Data Lake Architecture](images/Data%20Lake%20Architecture.svg)

Data Lake의 각 Layer는 다음의 기능을 수행한다.

|**Layer**|**설명**|
|---|---|
|Primitive Operation Layer|Java로 JDBC와 Hive, HDFS와 직접 접속하여 기본 기능을 처리한다. 호출을 위해서는 연결 정보와 SQL문등의 보다 구체적인 데이터가 필요하다. 대용량의 Bulk 처리에 있어서는 빠른 처리 속도가 필요한 부분도 있다.|
|Single Action Layer|RuleDB로 부터 생성된 설정 파일의 정보로부터 구체적인 연결 정보와 SQL문 등를 생성하여 Primitive Operation Layer를 호출한다. 기능에 따리서는 빠른 처리를 위해서 다중 쓰레드를 사용한 병렬 처리를 수행하는 부분도 있지만, 단일 기능에 대해서만 병렬 처리를 수행한다.|
|Batch & HealthCheck Layer|오른쪽정렬|
