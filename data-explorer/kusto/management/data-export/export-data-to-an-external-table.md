---
title: 외부 테이블로 데이터 내보내기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블로 데이터 내보내기에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: e26d1ae163b69f3a04c52538c245ea446dc11199
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521644"
---
# <a name="export-data-to-an-external-table"></a>외부 테이블로 데이터 내보내기

[외부 테이블을](../externaltables.md) 정의하고 데이터를 내보내 데이터를 내보낼 수 있습니다.
테이블 속성은 [외부 테이블을 만들](../externaltables.md#create-or-alter-external-table)때 지정되므로 내보내기 명령에 테이블의 속성을 포함할 필요가 없습니다. 내보내기 명령은 이름으로 외부 테이블을 참조합니다.
내보내기 데이터에는 [데이터베이스 관리자 권한이](../access-control/role-based-authorization.md)필요합니다.

> [!NOTE] 
> * 연결 문자열이 있는 `impersonate` 외부 테이블로 내보내는 것은 현재 지원되지 않습니다.

**구문:**

`.export`[`async` `to` `table` [ *외부 테이블 이름* <br>
[`with` `(` *속성 이름* `=` *속성값*`,`... `)`] <| *쿼리*

**출력:**

|출력 매개 변수 |Type |Description
|---|---|---
|외부 테이블 이름  |String |외부 테이블의 이름입니다.
|경로|String|출력 경로입니다.
|숫자 레코드|String| 경로로 내보낸 레코드 수입니다.

**참고:**
* 내보내기 쿼리 출력 스키마는 파티션에 의해 정의된 모든 열을 포함하여 외부 테이블의 스키마와 일치해야 합니다. 예를 들어 테이블이 *DateTime으로*분할된 경우 쿼리 출력 스키마에는 외부 테이블 분할 정의에 정의된 *TimestampColumnName과* 일치하는 타임스탬프 열이 포함되어야 합니다.

* 내보내기 명령을 사용하여 외부 테이블 속성을 재정의할 수 없습니다.
 예를 들어 Parquet 형식의 데이터를 CSV인 외부 테이블로 내보낼 수 없습니다.

* 다음 속성은 내보내기 명령의 일부로 지원됩니다. 자세한 내용은 [저장소로내보내기](export-data-to-storage.md) 섹션을 참조하십시오. 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 외부 테이블을 분할하면 내보낸 아티팩트는 [예제와](#partitioned-external-table-example)같이 파티션 정의에 따라 해당 디렉터리에 기록됩니다. 

* 파티션당 작성된 파일 수는 다음에 따라 다릅니다.
   * 외부 테이블에 datetime 파티션만 포함되거나 파티션이 전혀 없는 경우 기록된 파일 수(각 파티션에 대해 존재하는 경우)는 클러스터의 노드 수(또는 도달하는 경우 `sizeLimit` 더 많은 수)여야 합니다. 이는 내보내기 작업이 분산되어 클러스터의 모든 노드가 동시에 내보내기때문입니다. 
   배포를 사용하지 않도록 설정하려면 단일 노드만 `distributed` 쓰기를 수행하도록 false로 설정합니다. 이렇게 하면 파일이 적게 생성되지만 내보내기 성능이 저하됩니다.

   * 외부 테이블에 문자열 열로 파티션이 포함된 경우 내보낸 파일 수는 파티션당 단일 `sizeLimit` 파일(또는 도달한 경우)이어야 합니다. 모든 노드는 여전히 내보내기에 참여하지만(작업은 분산됨) 각 파티션은 특정 노드에 할당됩니다. 이 `distributed` 경우 false로 설정하면 단일 노드만 내보내기를 수행하지만 동작은 동일하게 유지됩니다(파티션당 작성된 단일 파일).

## <a name="examples"></a>예

### <a name="non-partitioned-external-table-example"></a>분할되지 않은 외부 테이블 예제

ExternalBlob은 분할되지 않은 외부 테이블입니다. 
```kusto
.export to table ExternalBlob <| T
```

|외부 테이블 이름|경로|숫자 레코드|
|---|---|---|
|외부블랍|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>분할된 외부 테이블 예제

분할된외부Blob은 다음과 같이 정의된 외부 테이블입니다. 

```
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

```
.export to table PartitionedExternalBlob <| T
```

|외부 테이블 이름|경로|숫자 레코드|
|---|---|---|
|외부블랍|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|외부블랍|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

키워드를 사용하여 `async` 명령이 비동기적으로 실행되는 경우 [표시 작업 세부 정보](../operations.md#show-operation-details) 명령을 사용하여 출력을 사용할 수 있습니다.