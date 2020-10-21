---
title: 쿼리 제한-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 제한에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: d0f815cd523e0e53111e791d8faaaf6c37c7bb7b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252836"
---
# <a name="query-limits"></a>쿼리 제한

Kusto는 큰 데이터 집합을 호스트 하 고 모든 관련 데이터를 메모리 내에 저장 하 여 쿼리를 충족 하려고 시도 하는 임시 쿼리 엔진입니다.
쿼리가 서비스 리소스를 제한 없이 독점할 하는 내재 된 위험이 있습니다. Kusto는 기본 쿼리 제한의 형태로 여러 기본 제공 보호 기능을 제공 합니다. 이러한 제한을 제거 하는 것을 고려 하는 경우 먼저이 작업을 수행 하 여 값을 얻을 수 있는지 여부를 확인 합니다.

## <a name="limit-on-query-concurrency"></a>쿼리 동시성 제한

**쿼리 동시성**  은 클러스터가 동시에 실행 되는 쿼리 수에 적용 되는 제한입니다.

* 쿼리 동시성 제한의 기본값은 실행 중인 SKU 클러스터에 따라 다르며로 계산 `Cores-Per-Node x 10` 됩니다.
  * 예를 들어 각 컴퓨터에 16 개 vCores가 있는 D14v2 SKU에서 설정 된 클러스터의 경우 기본 쿼리 동시성 제한은 `16 cores x10 = 160` 입니다.
* [쿼리 제한 정책을](../management/query-throttling-policy.md)구성 하 여 기본값을 변경할 수 있습니다. 
  * 클러스터에서 동시에 실행할 수 있는 실제 쿼리 수는 다양 한 요인에 따라 다릅니다. 가장 중요 한 요소는 클러스터 SKU, 클러스터 사용 가능 리소스 및 쿼리 패턴입니다. 쿼리 제한 정책은 프로덕션과 유사한 쿼리 패턴에서 수행 되는 부하 테스트에 따라 구성할 수 있습니다.

## <a name="limit-on-result-set-size-result-truncation"></a>결과 집합 크기 제한 (결과 잘림)

**결과 잘림** 은 쿼리에서 반환 하는 결과 집합에 대해 기본적으로 설정 되는 제한입니다. Kusto는 클라이언트에 반환 되는 레코드 수를 **50만**으로 제한 하 고 해당 레코드의 전체 데이터 크기를 **64 MB**로 제한 합니다. 이러한 제한 중 하나를 초과 하면 쿼리가 실패 하 고 "부분 쿼리 오류"가 발생 합니다. 전체 데이터 크기를 초과 하면 다음과 같은 메시지가 포함 된 예외가 생성 됩니다.

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

다음과 같은 예외를 제외 하 고 레코드 수를 초과 하면 실패 합니다.

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

이 오류를 처리 하는 방법에는 여러 가지가 있습니다.

* 쿼리를 수정 하 여 관심 있는 데이터만 반환 하도록 결과 집합 크기를 줄입니다. 이 전략은 초기 실패 쿼리가 너무 "넓은" 경우에 유용 합니다. 예를 들어 쿼리는 필요 하지 않은 데이터 열을 프로젝션 하지 않습니다.
* 집계와 같은 쿼리 후 처리를 쿼리 자체로 이동 하 여 결과 집합 크기를 줄입니다. 이 전략은 쿼리의 출력이 다른 처리 시스템에 공급 된 후 추가 집계를 수행 하는 시나리오에서 유용 합니다.
* 서비스에서 많은 데이터 집합을 내보내려는 경우 [데이터 내보내기를](../management/data-export/index.md) 사용 하 여 쿼리에서로 전환 합니다.
* `set` [클라이언트 요청 속성](../api/netfx/request-properties.md)에서 아래에 나열 된 문이나 플래그를 사용 하 여이 쿼리 제한을 표시 하지 않도록 서비스에 지시 합니다.

쿼리에 의해 생성 되는 결과 집합 크기를 줄이는 방법에는 다음이 포함 됩니다.

* [요약 연산자](../query/summarizeoperator.md) 그룹을 사용 하 고 쿼리 출력에서 비슷한 레코드를 집계 합니다. [모든 집계 함수](../query/any-aggfunction.md)를 사용 하 여 일부 열을 샘플링할 수 있습니다.
* [Take 연산자](../query/takeoperator.md) 를 사용 하 여 쿼리 출력을 샘플링 합니다.
* [Substring 함수](../query/substringfunction.md) 를 사용 하 여 넓은 자유 텍스트 열을 자릅니다.
* [Project 연산자](../query/projectoperator.md) 를 사용 하 여 결과 집합에서 필요 하지 않은 열을 삭제 합니다.

요청 옵션을 사용 하 여 결과 잘림을 사용 하지 않도록 설정할 수 있습니다 `notruncation` .
몇 가지 형태의 제한이 여전히 적용 되는 것이 좋습니다.

예를 들면 다음과 같습니다.

```kusto
set notruncation;
MyTable | take 1000000
```

값 `truncationmaxsize` (최대 데이터 크기 (바이트), 기본값: 64) 및 `truncationmaxrecords` (최대 레코드 수, 기본값: 50만)을 설정 하 여 결과 잘림을 보다 구체화 하는 것도 가능 합니다. 예를 들어 다음 쿼리는 1105 레코드 또는 1MB 중 하나에서 발생 하는 결과 잘림이 발생 하도록 설정 합니다.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

결과 잘림 제한을 제거 하면 Kusto에서 대량 데이터를 이동 하는 것을 의미 합니다.

`.export`명령을 사용 하거나 이후 집계를 위해 내보내기 목적으로 결과 잘림 제한을 제거할 수 있습니다. 나중에 집계를 선택 하는 경우 Kusto를 사용 하 여 집계 하는 것이 좋습니다.

Kusto는 호출자에 게 스트리밍하 여 "무한히 크게" 결과를 처리할 수 있는 여러 클라이언트 라이브러리를 제공 합니다. 이러한 라이브러리 중 하나를 사용 하 여 스트리밍 모드로 구성 합니다. 예를 들어 .NET Framework 클라이언트 (ExecuteQueryV2Async)를 사용 하 고 연결 문자열의 streaming 속성을 *true*로 설정 하거나 항상 결과를 스트리밍하는 *()* 호출을 사용 합니다.

결과 잘림는 클라이언트에 반환 되는 결과 스트림과 마찬가지로 기본적으로 적용 됩니다. 또한 동일한 영향을 주는 클러스터 간 쿼리에서 다른 클러스터에 대해 발생 하는 모든 하위 쿼리에 기본적으로 적용 됩니다.

## <a name="limit-on-memory-per-iterator"></a>반복기 당 메모리 제한

**결과 집합 반복기 당 최대 메모리** 는 Kusto에서 "런어웨이" 쿼리를 보호 하는 데 사용 하는 또 다른 한도입니다. 요청 옵션으로 표시 되는이 제한은 `maxmemoryconsumptionperiterator` 단일 쿼리 계획 결과 집합 반복기가 보유할 수 있는 메모리 양에 대 한 상한을 설정 합니다. 이 제한은와 같이 특성에 의해 스트리밍이 아닌 특정 반복기에 적용 됩니다 `join` . 이러한 상황이 발생 하는 경우 반환 되는 몇 가지 오류 메시지는 다음과 같습니다.

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

기본적으로이 값은 5gb로 설정 됩니다. 컴퓨터의 실제 메모리 절반까지이 값을 늘릴 수 있습니다.

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

대부분의 경우 데이터 집합을 샘플링 하 여이 제한을 초과 하는 것을 방지할 수 있습니다. 아래의 두 쿼리는 샘플링을 수행 하는 방법을 보여 줍니다. 첫 번째는 임의의 숫자 생성기를 사용 하는 통계 샘플링입니다. 두 번째는 데이터 집합의 일부 열 (일반적으로 일부 ID)을 해시 하 여 수행 되는 결정적 샘플링입니다.

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>노드당 메모리 제한

**노드당 최대 메모리** 는 "런어웨이" 쿼리를 보호 하는 데 사용 되는 또 다른 한도입니다. 요청 옵션으로 표시 되는이 제한은 `max_memory_consumption_per_query_per_node` 특정 쿼리에 대해 단일 노드에서 사용할 수 있는 메모리 양에 대 한 상한을 설정 합니다.

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>누적 문자열 집합 제한

다양 한 쿼리 작업에서 Kusto는 문자열 값을 "수집" 하 고 결과 생성을 시작 하기 전에 내부적으로 버퍼를 버퍼링 해야 합니다. 이러한 누적 문자열 집합은 크기와 해당 항목에서 보유할 수 있는 항목 수를 제한 합니다. 또한 각 개별 문자열은 특정 한도를 초과할 수 없습니다.
이러한 한도를 초과 하면 다음 오류 중 하나가 발생 합니다.

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

현재 최대 문자열 집합 크기를 늘리기 위한 스위치가 없습니다.
해결 방법으로, 버퍼링 되어야 하는 데이터의 양을 줄이기 위해 쿼리를 다르게 합니다. 조인 및 요약 등의 연산자에서 사용 하기 전에 불필요 한 열을 프로젝션 할 수 있습니다. 또는 [순서 섞기 쿼리](../query/shufflequery.md) 전략을 사용할 수 있습니다.

## <a name="limit-execution-timeout"></a>실행 제한 시간 제한

**서버 제한** 시간은 모든 요청에 적용 되는 서비스 측 시간 제한입니다.
실행 중인 요청에 대 한 시간 제한 (쿼리 및 제어 명령)은 Kusto의 여러 지점에서 적용 됩니다.

* 클라이언트 라이브러리 (사용 되는 경우)
* 요청을 수락 하는 서비스 끝점입니다.
* 요청을 처리 하는 서비스 엔진

기본적으로 쿼리는 시간 제한으로 4 분으로 설정 되 고 제어 명령의 경우 10 분으로 설정 됩니다. 필요한 경우이 값을 늘릴 수 있습니다 (1 시간 동안).

* Kusto. 탐색기를 사용 하 여 쿼리 하는 경우 **도구** &gt; **옵션** *  &gt; **연결** &gt; **쿼리 서버 제한 시간**을 사용 합니다.
* 프로그래밍 방식으로 `servertimeout` 클라이언트 요청 속성, 형식 값 (최대 1 시간)을 설정 합니다 `System.TimeSpan` .

**시간 제한에 대 한 참고 사항**

* 클라이언트 쪽에서는 응답이 클라이언트에 도착할 때까지 생성 되는 요청에서 시간 제한이 적용 됩니다. 클라이언트에서 페이로드를 읽는 데 걸리는 시간은 시간 제한의 일부로 처리 되지 않습니다. 이는 호출자가 스트림에서 데이터를 가져오는 속도에 따라 다릅니다.
* 또한 클라이언트 쪽에서 사용 되는 실제 제한 시간 값은 사용자가 요청한 서버 제한 시간 값 보다 약간 더 높습니다. 이러한 차이는 네트워크 대기 시간을 허용 하는 것입니다.
* 허용 되는 최대 요청 제한 시간을 자동으로 사용 하려면 클라이언트 요청 속성을 `norequesttimeout` 로 설정 `true` 합니다.

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here since it shouldn't be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>쿼리 CPU 리소스 사용량에 대 한 제한

Kusto를 사용 하면 쿼리를 실행 하 고 클러스터의 CPU 리소스를 많이 사용할 수 있습니다. 둘 이상의를 실행 하는 경우 쿼리 간에 공평 하 게 라운드 로빈 하려고 시도 합니다. 이 메서드는 임시 쿼리에 대해 최상의 성능을 생성 합니다.
다른 경우에는 특정 쿼리에 사용 되는 CPU 리소스를 제한 하는 것이 좋습니다. 예를 들어 "백그라운드 작업"을 실행 하는 경우 시스템은 대기 시간이 높아서 동시 임시 쿼리를 높은 우선 순위로 지정할 수 있습니다.

Kusto에서는 쿼리를 실행할 때 두 개의 [클라이언트 요청 속성](../api/netfx/request-properties.md) 을 지정할 수 있습니다. 속성은  *query_fanout_threads_percent* *query_fanout_nodes_percent*됩니다.
두 속성은 기본적으로 최대값 (100)으로 지정 되는 정수 이지만 특정 쿼리의 경우 다른 값으로 줄어들 수 있습니다. 

첫 번째 *query_fanout_threads_percent*는 스레드 사용에 대 한 fanout 요소를 제어 합니다. 100% 이면 클러스터는 각 노드에 모든 Cpu를 할당 합니다. 예: Azure D14 노드에 배포 된 클러스터의 Cpu 16 개. 50% 인 경우 Cpu의 절반이 사용 됩니다. 숫자는 전체 CPU로 반올림 되므로 0으로 설정 하는 것이 안전 합니다. 두 번째 *query_fanout_nodes_percent*는 하위 쿼리 배포 작업당 사용할 클러스터 노드 수를 제어 합니다. 비슷한 방식으로 작동 합니다.

## <a name="limit-on-query-complexity"></a>쿼리 복잡성에 대 한 제한

쿼리를 실행 하는 동안 쿼리 텍스트는 쿼리를 나타내는 관계형 연산자 트리로 변환 됩니다.
트리 깊이가 여러 천 수준의 내부 임계값을 초과 하는 경우 쿼리는 처리 하기에는 너무 복잡 한 것으로 간주 되며 오류 코드와 함께 실패 합니다. 이 오류는 관계형 연산자 트리가 제한을 초과 했음을 나타냅니다.
함께 연결 된 이항 연산자의 긴 목록이 포함 된 쿼리로 인해 한도가 초과 되었습니다. 예를 들면 다음과 같습니다.

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

이 특정 경우에는 연산자를 사용 하 여 쿼리를 다시 작성 합니다 [`in()`](../query/inoperator.md) .

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
