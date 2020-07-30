---
title: 포크 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 포크 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b234a95b4a541099f3fc050501ca6b0fd9f67ccf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348006"
---
# <a name="fork-operator"></a>fork 연산자

여러 소비자 연산자를 병렬로 실행 합니다.

## <a name="syntax"></a>구문

*T* `|` `fork` [*name* `=` ] `(` *하위 쿼리* `)` [*name* `=` ] `(` *하위 쿼리* `)` ...

## <a name="arguments"></a>인수

* *하위* 쿼리는 쿼리 연산자의 다운스트림 파이프라인입니다.
* *name* 은 하위 쿼리 결과 테이블의 임시 이름입니다.

## <a name="returns"></a>반환

각 하위 쿼리에 대해 하나씩 여러 개의 결과 테이블이 생성 됩니다.

**지원 되는 연산자**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**참고**

* [`materialize`](materializefunction.md)함수는 [`join`](joinoperator.md) 포크 다리에서 또는를 사용 하는 대신 사용할 수 있습니다 [`union`](unionoperator.md) .
입력 스트림은 구체화에 의해 캐시 되 고, 캐시 된 식은 조인/공용 구조체 다리에서 사용할 수 있습니다.

* 인수 또는 using 연산자에 의해 지정 되는 이름은 `name` [`as`](asoperator.md) 도구에서 결과 탭의 이름을 지정 하는 데 사용 됩니다 [`Kusto.Explorer`](../tools/kusto-explorer.md) .

* `fork`단일 하위 쿼리로를 사용 하지 마십시오.

* 연산자를 [batch](batches.md) [`materialize`](materializefunction.md) 통해 테이블 형식 식 문과 함께 batch를 사용 하는 것이 좋습니다 `fork` .

## <a name="examples"></a>예

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