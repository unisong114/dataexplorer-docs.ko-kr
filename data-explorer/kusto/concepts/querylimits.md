---
title: 쿼리 제한 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 쿼리 제한에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 0a25e0a4354798780b652861ba93494135b6d581
ms.sourcegitcommit: fd034cf3e3440dcbbbb8494eb4914572055afcee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759718"
---
# <a name="query-limits"></a>쿼리 제한

Kusto는 큰 데이터 세트를 호스트하고 모든 관련 데이터를 메모리 내에 보관하여 쿼리를 충족하려고 시도하는 임시 쿼리 엔진입니다.
쿼리가 서비스 리소스를 제한 없이 독점할 내재적 위험이 있습니다. Kusto는 기본 쿼리 제한의 형태로 여러 가지 기본 보호 기능을 제공합니다. 이러한 제한을 제거하여 얻을 수 있는 실질적인 이점이 있는지 확인한 후 제거하시기 바랍니다.

## <a name="limit-on-query-concurrency"></a>쿼리 동시성 제한

**쿼리 동시성** 은 클러스터가 동시에 실행되는 여러 쿼리에 적용하는 제한입니다.

* 쿼리 동시성 제한의 기본값은 실행 중인 SKU 클러스터에 따라 다르며, `Cores-Per-Node x 10`으로 계산합니다.
  * 예를 들어 머신마다 16개의 vCore가 있는 D14v2 SKU에 설정된 클러스터의 기본 쿼리 동시성 제한은 `16 cores x10 = 160`입니다.
* 기본값은 `default` 작업 그룹의 [요청 속도 제한 정책](../management/request-rate-limit-policy.md)을 구성하여 변경할 수 있습니다.
  * 클러스터에서 동시에 실행할 수 있는 실제 쿼리 수는 다양한 요인에 따라 달라집니다. 가장 중요한 요소는 클러스터 SKU, 클러스터의 가용 리소스 및 쿼리 패턴입니다. 쿼리 제한 정책은 프로덕션과 유사한 쿼리 패턴에서 수행되는 부하 테스트에 따라 구성할 수 있습니다.

## <a name="limit-on-result-set-size-result-truncation"></a>결과 세트 크기 제한(결과 잘림)

**결과 잘림** 은 쿼리에서 반환되는 결과 세트에 대해 기본적으로 설정되는 제한입니다. Kusto는 클라이언트에 반환되는 레코드 수를 **500,000** 개로 제한하고, 해당 레코드의 전체 데이터 크기를 **64MB** 로 제한합니다. 두 제한 중 하나라도 초과하면 "부분 쿼리 오류"와 함께 쿼리가 실패합니다. 전체 데이터 크기 제한을 초과하면 다음 메시지와 함께 예외가 생성됩니다.

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

레코드 수 제한을 초과하면 다음 예외와 함께 쿼리가 실패합니다.

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

이 오류를 처리하는 전략은 여러 가지가 있습니다.

* 관심 있는 데이터만 반환하도록 쿼리를 수정하여 결과 세트 크기를 줄입니다. 이 전략은 초기에 실패하는 쿼리가 너무 "방대한" 경우에 유용합니다. 예를 들어 쿼리는 필요 없는 데이터 열을 프로젝션하지 않습니다.
* 집계와 같은 쿼리 후 처리를 쿼리 자체로 이동하여 결과 세트 크기를 줄입니다. 이 전략은 쿼리의 출력을 다른 처리 시스템에 공급한 후 다른 집계를 수행하는 시나리오에서 유용합니다.
* 서비스에서 대량의 데이터 세트를 내보내려면 쿼리에서 [데이터 내보내기](../management/data-export/index.md) 사용으로 전환합니다.
* 아래에 나열된 `set` 문 또는 [클라이언트 요청 속성](../api/netfx/request-properties.md)의 플래그를 사용하여 이 쿼리 제한을 표시하지 않도록 서비스에 지시합니다.

쿼리에서 생성되는 결과 세트 크기를 줄이는 방법은 다음과 같습니다.

* [summarize 연산자](../query/summarizeoperator.md) 그룹을 사용하여 쿼리 출력의 비슷한 레코드를 집계합니다. [any 집계 함수](../query/any-aggfunction.md)를 사용하여 일부 열을 샘플링할 수도 있습니다.
* [take 연산자](../query/takeoperator.md)를 사용하여 쿼리 출력을 샘플링합니다.
* [substring 함수](../query/substringfunction.md)를 사용하여 넓은 자유 텍스트 열을 자릅니다.
* [project 연산자](../query/projectoperator.md)를 사용하여 결과 세트에서 필요 없는 열을 삭제합니다.

`notruncation` 요청 옵션을 사용하여 결과 잘림을 사용하지 않도록 설정할 수 있습니다.
어떤 형태로든 제한을 적용하는 것이 좋습니다.

다음은 그 예입니다. 

```kusto
set notruncation;
MyTable | take 1000000
```

`truncationmaxsize` 값(최대 데이터 크기(바이트 단위), 기본값은 64MB) 및 `truncationmaxrecords` 값(최대 레코드 수, 기본값은 500,000)을 설정하여 결과 잘림을 보다 구체적으로 제어하는 것도 가능합니다. 예를 들어 다음 쿼리는 레코드 1,105개 또는 1MB 제한 중 하나를 초과할 때 결과 잘림이 발생하도록 설정합니다.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

결과 잘림 제한을 제거한다는 것은 대량의 데이터를 Kusto로 보낸다는 뜻입니다.

`.export` 명령을 사용하여 내보내기를 수행하기 위해 또는 나중에 집계하기 위해 결과 잘림 제한을 제거할 수 있습니다. 나중에 집계하도록 선택하는 경우 Kusto를 사용하여 집계하는 것이 좋습니다.

Kusto는 결과를 호출자에게 스트리밍하여 "무한히 큰" 결과를 처리할 수 있는 여러 클라이언트 라이브러리를 제공합니다. 이러한 라이브러리 중 하나를 사용하고, 스트리밍 모드로 구성하세요. 예를 들어 .NET Framework 클라이언트(Microsoft.Azure.Kusto.Data)를 사용하고 연결 문자열의 스트리밍 속성을 *true* 로 설정하거나 항상 결과를 스트리밍하는 *ExecuteQueryV2Async()* 호출을 사용합니다.

결과 잘림은 클라이언트에 반환되는 결과 스트림에만 적용되는 것이 아니라 기본적으로 적용됩니다. 또한 클러스터 간 쿼리에서 한 클러스터가 다른 클러스터에 대해 실행하는 모든 하위 쿼리에도 기본적으로 적용되며, 효과는 비슷합니다.

### <a name="setting-multiple-result-truncation-properties"></a>여러 결과 잘림 속성 설정

`set` 문을 사용하는 경우 및/또는 [클라이언트 요청 속성](../api/netfx/request-properties.md)에서 플래그를 지정하는 경우 다음이 적용됩니다.

* `notruncation`이 설정되고 `truncationmaxsize`, `truncationmaxrecords` 또는 `query_take_max_records`도 설정된 경우 `notruncation`이 무시됩니다.
* `truncationmaxsize`, `truncationmaxrecords` 및/또는 `query_take_max_records`가 여러 번 설정된 경우 각 속성에 대해 *더 낮은* 값이 적용됩니다.

## <a name="limit-on-memory-per-iterator"></a>반복기당 메모리 제한

**결과 세트 반복기당 최대 메모리** 는 Kusto에서 "런어웨이" 쿼리를 보호하는 데 사용하는 또 다른 제한입니다. 요청 옵션 `maxmemoryconsumptionperiterator`로 표시되는 이 제한은 단일 쿼리 계획 결과 세트 반복기가 보관할 수 있는 메모리 양의 상한을 설정합니다. 이 제한은 `join`처럼 기본적으로 스트리밍하지 않는 특정 반복기에 적용됩니다. 이 상황이 발생하면 다음과 같은 오류 메시지가 반환됩니다.

```
The ClusterBy operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete E_RUNAWAY_QUERY.

The DemultiplexedResultSetCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The ExecuteAndCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Sort operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Summarize operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNestedAggregator operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNested operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).
```

기본적으로 이 값은 5GB로 설정됩니다. 머신의 실제 메모리 절반까지 이 값을 높일 수 있습니다.

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

대부분의 경우 데이터 세트를 샘플링하면 이 제한을 초과하는 것을 방지할 수 있습니다. 아래의 두 쿼리는 샘플링 방법을 보여줍니다. 첫 번째는 임의의 숫자 생성기를 사용하는 통계 샘플링입니다. 두 번째는 데이터 세트의 일부 열(일반적으로 일부 ID)을 해시하여 수행되는 결정적 샘플링입니다.

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

클라이언트 요청 속성과 `set` 문을 사용할 때 `maxmemoryconsumptionperiterator`가 여러 번 설정된 경우 더 낮은 값이 적용됩니다.


## <a name="limit-on-memory-per-node"></a>노드당 메모리 제한

**노드당 쿼리별 최대 메모리** 는 "런어웨이" 쿼리를 보호하는 데 사용하는 또 다른 제한입니다. 요청 옵션 `max_memory_consumption_per_query_per_node`로 표시되는 이 제한은 단일 노드에서 특정 쿼리에 사용할 수 있는 메모리 양의 상한을 설정합니다.

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

클라이언트 요청 속성과 `set` 문을 사용할 때 `max_memory_consumption_per_query_per_node`가 여러 번 설정된 경우 더 낮은 값이 적용됩니다.

## <a name="limit-on-accumulated-string-sets"></a>누적 문자열 세트 제한

다양한 쿼리 작업에서 Kusto는 문자열 값을 "수집"하고 내부적으로 버퍼링해야만 결과 생성을 시작할 수 있습니다. 이러한 누적 문자열 세트의 크기와 보관할 수 있는 항목 수에 제한이 있습니다. 또한 각 문자열은 특정 제한을 초과하면 안 됩니다.
이러한 제한 중 하나를 초과하면 다음 오류 중 하나가 발생합니다.

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

현재는 최대 문자열 세트 크기를 늘리는 스위치가 없습니다.
해결 방법으로, 버퍼링해야 하는 데이터 양을 줄이도록 쿼리를 다시 작성해야 합니다. 불필요한 열을 프로젝션한 후 join이나 summarize 같은 연산자에 사용할 수 있습니다. 또는 [쿼리 순서 섞기](../query/shufflequery.md) 전략을 사용할 수 있습니다.

## <a name="limit-execution-timeout"></a>실행 시간 제한

**서버 시간 제한** 은 모든 요청에 적용되는 서비스 쪽 시간 제한입니다.
실행 중인 요청(쿼리 및 제어 명령)에 대한 시간 제한은 다음과 같은 Kusto의 여러 지점에서 적용됩니다.

* 클라이언트 라이브러리(사용되는 경우)
* 요청을 수락하는 서비스 엔드포인트
* 요청을 처리하는 서비스 엔진

기본적으로 쿼리의 시간 제한은 4분, 제어 명령의 시간 제한은 10분으로 설정됩니다. 필요한 경우 이 값을 늘릴 수 있습니다(1시간으로 제한).

* Kusto.Explorer를 사용하여 쿼리하는 경우 **도구** &gt; **옵션** _ &gt; _ *연결** &gt; **쿼리 서버 시간 제한** 을 사용합니다.
* `System.TimeSpan` 형식의 값인 `servertimeout` 클라이언트 요청 속성을 프로그래밍 방식으로 최대 1시간까지 설정합니다.

**시간 제한에 대한 참고 사항**

* 클라이언트 쪽에서는 응답이 클라이언트에 도착할 때까지 생성되는 요청에서 시간 제한이 적용됩니다. 클라이언트에서 페이로드를 읽는 데 걸리는 시간은 시간 제한에 포함되지 않습니다. 이 시간은 호출자가 스트림에서 데이터를 가져오는 속도에 따라 달라집니다.
* 또한 클라이언트 쪽에서 사용되는 실제 시간 제한 값은 사용자가 요청한 서버 시간 제한 값보다 약간 높습니다. 이러한 차이는 네트워크 대기 시간을 허용하기 위한 것입니다.
* 허용되는 최대 요청 시간 제한을 자동으로 사용하려면 클라이언트 요청 속성 `norequesttimeout`을 `true`로 설정합니다.

## <a name="limit-on-query-cpu-resource-usage"></a>쿼리 CPU 리소스 사용량 제한

Kusto에서는 쿼리를 실행하고 클러스터가 보유한 CPU 리소스를 모두 사용할 수 있습니다. Kusto는 두 개 이상의 쿼리가 실행 중일 때 쿼리 간에 공평한 라운드 로빈을 시도합니다. 이 방법은 임시 쿼리의 성능을 극대화합니다.
특정 쿼리에 사용되는 CPU 리소스를 제한하고 싶은 경우가 있습니다. 예를 들어 "백그라운드 작업"을 실행할 때 시스템에서는 동시 임시 쿼리에 높은 우선 순위를 부여하기 위해 높은 대기 시간을 허용해야 할 수 있습니다.

Kusto는 쿼리를 실행할 때 두 [클라이언트 요청 속성](../api/netfx/request-properties.md)을 지정할 수 있도록 지원합니다.
속성은 *query_fanout_threads_percent* 및 *query_fanout_nodes_percent* 입니다.
두 속성은 기본적으로 최댓값(100)으로 지정되는 정수이지만, 쿼리에 따라 다른 값으로 줄일 수 있습니다.

첫 번째 속성 *query_fanout_threads_percent* 는 스레드 사용에 대한 팬아웃 요소를 제어합니다.
이 속성이 100%로 설정된 경우 클러스터는 각 노드의 모든 CPU를 할당합니다. 예를 들어 한 클러스터의 CPU 16개가 Azure D14 노드에 배포됩니다.
이 속성이 50%로 설정된 경우에는 CPU의 절반이 사용됩니다.
숫자는 전체 CPU로 반올림되므로 속성 값을 0으로 설정하는 것이 안전합니다.

두 번째 속성 *query_fanout_nodes_percent* 는 하위 쿼리 배포 작업에 사용할 클러스터의 노드 수를 제어합니다.
작동 방식은 비슷합니다.

클라이언트 요청 속성과 `set` 문을 사용할 때 `query_fanout_nodes_percent` 또는 `query_fanout_threads_percent`가 여러 번 설정된 경우 더 낮은 값이 적용됩니다.

## <a name="limit-on-query-complexity"></a>쿼리 복잡성 제한

쿼리를 실행하는 동안 쿼리 텍스트는 쿼리를 나타내는 관계형 연산자 트리로 변환됩니다.
트리 깊이가 수천 레벨의 내부 임계값을 초과하면 처리하기에 너무 복잡한 쿼리로 간주되고 오류 코드와 함께 쿼리가 실패합니다. 이 실패는 관계형 연산자 트리가 제한을 초과했음을 나타냅니다.
함께 연결된 이진 연산자의 긴 목록이 포함된 쿼리로 인해 제한을 초과했습니다. 다음은 그 예입니다. 

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

이 예제에서는 [`in()`](../query/inoperator.md) 연산자를 사용하여 쿼리를 다시 작성합니다.

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
