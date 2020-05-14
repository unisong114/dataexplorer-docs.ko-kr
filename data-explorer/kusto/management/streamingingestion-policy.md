---
title: Kusto streaming 수집 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 스트리밍 수집 정책 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1c3ce0c0d383d07375333b08de336503d1578b1a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83381998"
---
# <a name="streaming-ingestion-policy-management"></a>스트리밍 수집 정책 관리

스트리밍 수집 정책을 데이터베이스 또는 테이블에 연결 하 여 해당 위치에 대 한 스트리밍 수집을 허용할 수 있습니다. 또한이 정책은 스트리밍 수집에 사용 되는 행 저장소를 정의 합니다.

스트리밍 수집에 대 한 자세한 내용은 [스트리밍 수집 (미리 보기)](../../ingest-data-streaming.md)을 참조 하세요. 스트리밍 수집 정책에 대 한 자세한 내용은 스트리밍 수집 [정책](streamingingestionpolicy.md)을 참조 하세요.

## <a name="show-policy-streamingingestion"></a>. 정책 streamingingestion을 표시 합니다.

이 `.show policy streamingingestion` 명령은 데이터베이스 또는 테이블의 스트리밍 수집 정책을 보여 줍니다.

**구문**

`.show``database` `policy` `streamingingestion` 
 Mydatabase `.show` `table`MyTable `policy``streamingingestion`

**반환**

이 명령은 다음 열이 있는 테이블을 반환 합니다.

|Column    |형식    |Description
|---|---|---
|PolicyName|`string`|정책 이름-StreamingIngestionPolicy
|EntityName|`string`|데이터베이스 또는 테이블 이름
|정책    |`string`|스트리밍 수집 정책 [개체로](#streaming-ingestion-policy-object) 형식이 지정 된 스트리밍 수집 정책을 정의 하는 JSON 개체입니다.

**예제**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|정책|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"NumberOfRowStores": 4}

### <a name="streaming-ingestion-policy-object"></a>스트리밍 수집 정책 개체

|속성  |형식    |Description                                                       |
|----------|--------|------------------------------------------------------------------|
|NumberOfRowStores |`int`  |엔터티에 할당 된 행 저장소 수|
|SealIntervalLimit|`TimeSpan?`|테이블에서 밀봉 작업 사이의 간격에 대 한 제한 (옵션)입니다. 유효한 범위는 1 시간에서 24 시간 사이입니다. 기본값: 24시간.|
|SealThresholdBytes|`int?`|테이블에 대 한 단일 봉인 작업에 사용할 데이터 크기 제한 (옵션)입니다. 값의 유효한 범위는 10에서 200 Mb 사이입니다. 기본값: 200 Mb|

## <a name="alter-policy-streamingingestion"></a>. 변경 정책 streamingingestion

이 `.alter policy streamingingestion` 명령은 데이터베이스 또는 테이블의 streamingingestion 정책을 설정 합니다.

**구문**

* `.alter``database`Mydatabase `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``database`Mydatabase `policy` `streamingingestion``enable`

* `.alter``database`Mydatabase `policy` `streamingingestion``disable`

* `.alter``table` `policy` MyTable `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``table` `policy` MyTable `streamingingestion``enable`

* `.alter``table` `policy` MyTable `streamingingestion``disable`

**참고 사항**

1. *StreamingIngestionPolicyObject* 은 스트리밍 수집 정책 개체가 정의 된 JSON 개체입니다.

2. `enable`-정책이 정의 되지 않았거나 0 rowstores를 사용 하 여 이미 정의 되어 있는 경우 스트리밍 수집 정책을 4 개의 rowstores로 설정 합니다. 그렇지 않으면 명령은 아무 작업도 수행 하지 않습니다.

3. `disable`-정책이 양의 rowstores로 이미 정의 된 경우 0 rowstores를 사용 하 여 스트리밍 수집 정책을로 설정 합니다. 그렇지 않으면 명령이 아무 작업도 수행 하지 않습니다.

**예제**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>. 정책 streamingingestion 삭제

이 `.delete policy streamingingestion` 명령은 데이터베이스 또는 테이블에서 streamingingestion 정책을 삭제 합니다.

**구문** 

`.delete``database`Mydatabase `policy``streamingingestion`

`.delete``table`MyTable `policy``streamingingestion`

**반환**

이 명령은 테이블이 나 데이터베이스 streamingingestion 정책 개체를 삭제 한 다음 해당 하는 [. show policy streamingingestion](#show-policy-streamingingestion) 명령의 출력을 반환 합니다.

**예제**

```kusto
.delete database MyDatabase policy streamingingestion 
```