# Data Lake Architecture

펜타시스템테크놀러지의 Data Lake의 구조은 아래와 같다.

![Data Lake Architecture](images/Data%20Lake%20Architecture.svg)

펜타시스템테크놀러지의 Data Lake의 각 Layer는 다음의 기능을 수행한다.

|**Layer**|**설명**|
|---|---|
|Primitive Operation Layer|Java로 JDBC와 Hive, HDFS와 직접 접속하여 기본 기능을 처리한다. 호출을 위해서는 연결 정보와 SQL문등의 보다 구체적인 데이터가 필요하다. 대용량의 Bulk 처리에 있어서는 빠른 처리 속도가 필요한 부분도 있다.|
|Single Action Layer|RuleDB로 부터 생성된 설정 파일의 정보로부터 구체적인 연결 정보와 SQL문 등를 생성하여 Primitive Operation Layer를 호출한다. 기능에 따리서는 빠른 처리를 위해서 다중 쓰레드를 사용한 병렬 처리를 수행하는 부분도 있지만, 단일 기능에 대해서만 병렬 처리를 수행한다.|
|Batch & HealthCheck Layer|배치의 수행 전 이중화된 접속 자원 중 사용 가능한 접속 자원에 대한 Health Check를 수행하여 Batch시 사용할 접속 자원을 선택한다. 수집 대상 테이블 혹은 OpenAPI를 병렬 처리로 수집한다. 각 테이블/OpenAPI별 수행 시에 필요한 Single Action들을 순차적으로 수행한다. 수집 로그는 RuleDB에 Log 테이블에 기록된다.|
|Scheduler Layer|매일 정해진 시간에 HealthCheck와 Batch를 수행한다. 실패한 항목이 있는 경우 HealthCheck와 Batch를 재수행하여 실패한 항목만 다시 수집한다.|
|Dashboard Layer|RuleDB에 저장된 배치 수행 Log 테이블을 조회하여, 수집 현황을 Web으로 표현한다.|

각 Layer별로 제공하는 기능은 아래와 같다.

## Primitive Operation

* JDBC로 SELECT문을 수행 결과를 추출하여 SequenceFile로 HDFS에 적재
* JDBC로 SELECT문을 수행 결과를 추출하여 CSV 파일로 저장
* JDBC로 SELECT문을 수행 결과를 추출하여 JDBC로 DBMS의 테이블에 적재
* CSV 파일을 SequenceFile로 HDFS에 적재
* CSV 파일을 JDBC로 DBMS의 INSERT문으로 적재
* JDBC로 DBMS의 Product와 Version 조회
* JDBC로 DBMS의 Query를 조회하여 결과를 화면에 출력
* JDBC로 DBMS의 Query의 결과 데이터형을 조회
* JSON으로 전달된 Query를 JDBC로 수행하고 결과를 JSON으로 반환
* JAR 파일의 재빌드 없이 JDBC Driver 추가로 새로운 RDBMS를 사용 가능

## Single Action

* RuleDB에서 단일 시스템의 모든 테이블을 정보를 조회하여 설정 파일을 생성
* 단일 CSV 파일을 JDBC로 DBMS의 테이블에 적재
* 단일 CSV 파일을 SequenceFile로 HDFS(Hive)에 적재
* Hive 단일 테이블을 추출하여 CSV 파일로 저장
* Hive 단일 시스템 복수 테이블의 테이블/파티션의 통계 정보를 분석
* Hive 단일 시스템 복수 테이블의 불필요한 파티션을 삭제
* Hive 단일 시스템 복수 테이블의 테이블/파티션의 레코드 건수를 조회
* Hive 단일 시스템 복수 테이블을 생성
* Hive 단일 시스템 복수 테이블의 파티션을 생성
* Hive 단일 시스템 복수 테이블을 제거
* Hive 단일 시스템 복수 테이블의 파티션을 제거
* Hive 단일 시스템 복수 테이블의 파티션 목록을 조회
* Hive 단일 시스템의 쿼리를 수행
* Hive 단일 시스템의 SQL Shell을 수행
* Hive 단일 시스템 복수 테이블의 모든 레코드를 삭제
* Hive 단일 시스템 복수 테이블의 적재 데이터 검증(MD5)
* RDBMS 단일 테이블을 추출하여 CSV 파일로 저장
* RDBMS 단일 테이블을 추출하여 Hive의 테이블/파티션에 적재
* RDBMS 단일 시스템 복수 테이블의 테이블/파티션의 레코드 건수를 조회
* RDBMS 단일 시스템 복수 테이블의 모든 레코드를 삭제
* RDBMS의 단일 테이블을 추출하여 RDBMS의 단일 테이블에 적재
* RDBMS 단일 시스템의 쿼리를 수행
* RDBMS 단일 시스템의 SQL Shell을 수행
* OpenAPI 수집(HTTP) API 제공 및 Python 변수(List)를 Hive 단일 테이블/파티션에 적재
* JDBC 비밀번호 암복호화

## Batch & HealthCheck

* Health Check로 사용 가능한 RuleDB(PostgreSQL), Hive Server, HDFS Name Server, Proxy(Apache2) Server를 선택
* RDBMS에서 Hive로 복수의 시스템의 복수 테이블을 수집/적재
* OpenAPI에서 Hive로 복수의 시스템의 수집/적재
* 수집 결과를 Rule DB의 Log 테이블로 기록

## Scheduler

* 정해진 시간에 Health Check와 Batch 수행
* Batch 수행 실패 시 실패한 시스템/테이블만 Health Check와 Batch 재수행

## Dashboard

* 수집 현황을 Web 화면으로 출력
