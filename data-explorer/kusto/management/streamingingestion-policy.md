---
title: 스트리밍 수집 정책 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 스트리밍 수집 정책 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b0b1a76e52688dcc88ca87023309f9c970b4c702
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519621"
---
# <a name="streaming-ingestion-policy-management"></a>스트리밍 인식 정책 관리

스트리밍 허용 정책은 데이터베이스 또는 테이블에 연결하여 해당 위치에 대한 스트리밍 구성을 허용할 수 있습니다. 또한 이 정책은 스트리밍 사용에 사용되는 행 저장소를 정의합니다.

스트리밍 수집에 대한 자세한 내용은 [스트리밍 수집(미리 보기)을](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)참조하십시오. 스트리밍 섭취 정책에 대한 자세한 내용은 [스트리밍 섭취 정책을](streamingingestionpolicy.md)참조하세요.

## <a name="show-policy-streamingingestion"></a>정책 스트리밍 을 표시합니다.

`.show policy streamingingestion` 이 명령은 데이터베이스 또는 테이블의 스트리밍 구성 정책을 보여 주며,

**구문**

`.show``database` `policy` `streamingingestion` 마이데이터베이스 
 `.show` `policy` `table` 마이테이블`streamingingestion`

**반환**

이 명령은 다음 열이 있는 테이블을 반환합니다.

|열    |Type    |Description
|---|---|---
|PolicyName|`string`|정책 이름 - 스트리밍 정책
|EntityName|`string`|데이터베이스 또는 테이블 이름
|정책    |`string`|스트리밍 인식 정책 개체로 서식이 지정된 스트리밍 인식 정책을 정의하는 JSON [개체](#streaming-ingestion-policy-object)

**예제**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|정책|자식 엔터티|EntityType|
|---|---|---|---|---|
|스트리밍정책|DB1|{ "숫자OfRow스토어": 4 }

### <a name="streaming-ingestion-policy-object"></a>스트리밍 인식 정책 개체

|속성  |Type    |Description                                                       |
|----------|--------|------------------------------------------------------------------|
|숫자OfRow스토어 |`int`  |엔터티에 할당된 행 저장소 수|
|밀봉 간격 제한|`TimeSpan?`|테이블의 씰 작업 사이의 간격에 대한 선택적 제한입니다. 유효한 범위는 1~24시간 사이입니다. 기본값: 24시간.|
|씰임계값스바이트|`int?`|테이블에서 단일 씰 작업을 위해 데이터 양에 대한 선택적 제한. 값의 유효한 범위는 10~200MB사이입니다. 기본값: 200M.|

## <a name="alter-policy-streamingingestion"></a>정책 스트리밍 변경..변경

`.alter policy streamingingestion` 명령은 데이터베이스 또는 테이블의 스트리밍 정책을 설정합니다.

**구문**

* `.alter``database` 마이데이터베이스 `policy` `streamingingestion` *스트리밍정책대상*

* `.alter``database` 마이 `policy` `streamingingestion` 데이터베이스`enable`

* `.alter``database` 마이 `policy` `streamingingestion` 데이터베이스`disable`

* `.alter``table` 마이테이블 `policy` `streamingingestion` *스트리밍정책대상*

* `.alter``table` 마이테이블 `policy` `streamingingestion` (것)과 함께`enable`

* `.alter``table` 마이테이블 `policy` `streamingingestion` (것)과 함께`disable`

**참고 사항**

1. *스트리밍통합정책Object는* 스트리밍 통합 정책 개체가 정의된 JSON 개체입니다.

2. `enable`- 정책이 정의되지 않았거나 0 rowstore로 이미 정의된 경우 스트리밍 통합 정책을 4 행 저장소로 설정하면 명령은 아무 것도 수행하지 않습니다.

3. `disable`- 정책이 이미 양수 rowstore로 정의된 경우 스트리밍 통합 정책을 0 rowstore로 설정하면 명령이 아무 것도 수행하지 않습니다.

**예제**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>.delete 정책 스트리밍

명령은 `.delete policy streamingingestion` 데이터베이스 또는 테이블에서 스트리밍 정책을 삭제합니다.

**구문** 

`.delete``database` 마이 `policy` 데이터베이스`streamingingestion`

`.delete``table` 마이테이블 `policy` (것)과 함께`streamingingestion`

**반환**

명령은 테이블 또는 데이터베이스 스트리밍 정책 개체를 삭제한 다음 해당 [.show 정책 스트리밍 명령의](#show-policy-streamingingestion) 출력을 반환합니다.

**예제**

```kusto
.delete database MyDatabase policy streamingingestion 
```