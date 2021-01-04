# Data Lake Architecture

Data Lake의 구조은 아래와 같다.

![Data Lake Architecture](images/Data%20Lake%20Architecture.svg)

Data Lake의 각 Layer는 다음의 기능을 수행한다.

|**Layer**|**설명**|
|---|---|
|Primitive Operation Layer|Java로 JDBC와 Hive, HDFS와 직접 접속하여 기본 기능을 처리한다. 대용량의 Bulk 처리에 있어서는 빠른 처리 속도가 필요한 부분도 있다.|
|왼쪽정렬|오른쪽정렬|
|왼쪽정렬|오른쪽정렬|
