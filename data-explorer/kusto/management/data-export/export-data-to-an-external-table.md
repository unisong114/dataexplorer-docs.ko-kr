---
title: 외부 테이블로 데이터 내보내기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블로 데이터를 내보내는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 56150c480d0d5ecfd4d428e51f7bdb4b68e36b0c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617700"
---
# <a name="export-data-to-an-external-table"></a>외부 테이블로 데이터 내보내기

[외부 테이블](../externaltables.md) 을 정의 하 고 데이터를 내보내 데이터를 내보낼 수 있습니다.
테이블 속성은 [외부 테이블을 만들](../externaltables.md#create-or-alter-external-table)때 지정 되므로 내보내기 명령에 테이블의 속성을 포함할 필요가 없습니다. 내보내기 명령은 이름별로 외부 테이블을 참조 합니다.
데이터 내보내기에는 [데이터베이스 관리자 권한이](../access-control/role-based-authorization.md)필요 합니다.

> [!NOTE] 
> * 연결 문자열을 사용 `impersonate` 하는 외부 테이블로 내보내기는 현재 지원 되지 않습니다.

**구문:**

`.export`[`async`] `to` `table` *Externaltablename* <br>
[`with` `(` *PropertyName* PropertyName `=` *PropertyValue*PropertyValue`,`... `)`] <| *쿼리*

**출력**

|출력 매개 변수 |Type |설명
|---|---|---
|ExternalTableName  |String |외부 테이블의 이름입니다.
|경로|String|출력 경로입니다.
|NumRecords|String| 경로로 내보낸 레코드 수입니다.

**참고:**
* 내보내기 쿼리 출력 스키마는 파티션에서 정의한 모든 열을 포함 하 여 외부 테이블의 스키마와 일치 해야 합니다. 예를 들어 테이블을 *DateTime*으로 분할 하는 경우 쿼리 출력 스키마는 외부 테이블 분할 정의에 정의 된 *TimestampColumnName* 일치 하는 타임 스탬프 열을 포함 해야 합니다.

* 내보내기 명령을 사용 하 여 외부 테이블 속성을 재정의할 수는 없습니다.
 예를 들어 데이터 형식이 CSV 인 외부 테이블로 Parquet 형식의 데이터를 내보낼 수 없습니다.

* 다음 속성은 내보내기 명령의 일부로 지원 됩니다. 자세한 내용은 [저장소로 내보내기](export-data-to-storage.md) 섹션을 참조 하세요. 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 외부 테이블이 분할 된 경우 내보낸 아티팩트는 [예제](#partitioned-external-table-example)에 표시 된 파티션 정의에 따라 해당 디렉터리에 기록 됩니다. 

* 파티션당 작성 되는 파일 수는 다음에 따라 달라 집니다.
   * 외부 테이블에 datetime 파티션만 포함 되어 있거나 파티션이 없는 경우 (각 파티션에 대해 기록 된 파일 수)는 클러스터의 노드 수를 기준으로 해야 합니다 (또는에 도달 하는 경우 `sizeLimit` ). 이는 클러스터의 모든 노드가 동시에 내보내기 때문에 내보내기 작업이 분산 되기 때문입니다. 
   배포를 사용 하지 않도록 설정 하 여 단일 노드만 쓰기를 수행 하도록 하려면 `distributed` 를 false로 설정 합니다. 이렇게 하면 더 작은 파일을 만들지만 내보내기 성능이 저하 됩니다.

   * 외부 테이블에 문자열 열을 기준으로 하는 파티션이 포함 된 경우 내보낸 파일의 수는 파티션당 단일 파일 이어야 합니다 (또는에 도달 하 `sizeLimit` 는 경우). 모든 노드는 내보내기 (작업 분산)에 계속 참여 하지만 각 파티션은 특정 노드에 할당 됩니다. 이 `distributed` 경우를 false로 설정 하면 단일 노드만 내보내기를 수행 하지만 동작은 동일 하 게 유지 됩니다 (파티션당 단일 파일 기록).

## <a name="examples"></a>예

### <a name="non-partitioned-external-table-example"></a>분할 되지 않은 외부 테이블 예제

ExternalBlob은 분할 되지 않은 외부 테이블입니다. 
```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|경로|NumRecords|
|---|---|---|
|ExternalBlob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>분할 된 외부 테이블 예제

PartitionedExternalBlob는 다음과 같이 정의 되는 외부 테이블입니다. 

```kusto
.create external table PartitionedExternalBlob (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

```kusto
.export to table PartitionedExternalBlob <| T
```

|ExternalTableName|경로|NumRecords|
|---|---|---|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

`async` 키워드를 사용 하 여 명령을 비동기적으로 실행 하는 경우 [작업 세부 정보 표시](../operations.md#show-operation-details) 명령을 사용 하 여 출력을 사용할 수 있습니다.
