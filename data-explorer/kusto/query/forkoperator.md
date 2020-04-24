---
title: 포크 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 fork 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 13cd837b3874a55ec758991f5609e089daba7c75
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515201"
---
# <a name="fork-operator"></a>fork 연산자

여러 소비자 연산자들을 병렬로 실행합니다.

**구문**

*T* `|` `=``(`*subquery* `)` `(`*subquery* `)` *name*`=`*name*[ 이름 ] 하위 쿼리 [ 이름 ] 하위 쿼리 ... `fork`

**인수**

* *하위 쿼리는* 쿼리 연산자의 다운스트림 파이프라인입니다.
* *이름은* 하위 쿼리 결과 테이블의 임시 이름입니다.

**반환**

각 하위 쿼리에 대해 하나씩 여러 결과 테이블입니다.

**지원되는 연산자**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**참고 사항**

* [`materialize`](materializefunction.md)기능을 사용하거나 [`join`](joinoperator.md) [`union`](unionoperator.md) 포크 다리를 대체하는 데 사용할 수 있습니다.
입력 스트림은 구체화하여 캐시된 다음 캐시된 식을 조인/공용 구조체 레그에서 사용할 수 있습니다.

* `name` 인수 또는 연산자를 사용하여 [`as`](asoperator.md) 지정한 이름은 [`Kusto.Explorer`](../tools/kusto-explorer.md) 도구의 결과 탭 이름을 지정하는 데 사용됩니다.

* 단일 `fork` 하위 쿼리를 사용하지 마십시오.

* 연산자보다 [`materialize`](materializefunction.md) `fork` 테이블 식 문으로 [일괄 처리를](batches.md) 사용하는 것을 선호합니다.

**예**

```kusto
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 )
    ( project Timestamp, EventText | top 1000 by Timestamp desc)
    ( summarize min(Timestamp), max(Timestamp) by ActivityID )
 
// In the following examples the result tables will be named: Errors, EventsTexts and TimeRangePerActivityID
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 | as Errors )
    ( project Timestamp, EventText | top 1000 by Timestamp desc | as EventsTexts )
    ( summarize min(Timestamp), max(Timestamp) by ActivityID | as TimeRangePerActivityID )
    
 KustoLogs
| where Timestamp > ago(1h)
| fork
    Errors = ( where Level == "Error" | project EventText | limit 100 )
    EventsTexts = ( project Timestamp, EventText | top 1000 by Timestamp desc )
    TimeRangePerActivityID = ( summarize min(Timestamp), max(Timestamp) by ActivityID )
```