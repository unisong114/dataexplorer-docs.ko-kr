---
title: 쿼리 제한 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 제한에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 437e9781b9e29db7496292ba6a416f6e0c769e8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523072"
---
# <a name="query-limits"></a>쿼리 제한

Kusto는 대용량 데이터 집합을 호스팅하고 모든 관련 데이터를 메모리에 유지하여 쿼리를 충족시키려는 임시 쿼리 엔진이므로 쿼리가 경계 없이 서비스 리소스를 독점할 위험이 내재되어 있습니다. Kusto는 기본 쿼리 제한의 형태로 여러 기본 제공 보호 기능을 제공합니다.

## <a name="limit-on-query-concurrency"></a>쿼리 동시성 제한

**쿼리 동시성은** 클러스터가 동시에 실행되는 여러 쿼리에 부과하는 제한입니다.
쿼리 동시성 제한의 기본값은 실행 중인 SKU 클러스터에 따라 달라지며 다음과 `Cores-Per-Node x 10`같이 계산됩니다. 예를 들어 각 컴퓨터에 16개의 vCore가 있는 D14v2 SKU에 설정된 클러스터의 경우 기본 쿼리 `16 cores x10 = 160`동시성 제한이 됩니다.
기본값은 지원 티켓을 만들어 변경할 수 있습니다. 나중에이 컨트롤은 컨트롤 명령을 통해 노출됩니다.

## <a name="limit-on-result-set-size-result-truncation"></a>결과 세트 크기 제한(결과 잘림)

**결과 잘림은** 쿼리에서 반환되는 결과 집합에 기본적으로 설정된 제한입니다. Kusto는 클라이언트에 반환되는 레코드 수를 **500,000개로**제한하고 해당 레코드의 전체 메모리를 **64MB로**제한합니다. 이러한 제한 중 하나를 초과하면 "부분 쿼리 실패"로 쿼리가 실패합니다. 전체 메모리를 초과하면 다음과 같은 메시지에 예외가 생성됩니다.

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

레코드 수를 초과하면 다음과 같은 예외를 제외하고 실패합니다.

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

이 오류를 처리하기 위한 여러 가지 전략이 있습니다.

* 쿼리를 수정하여 흥미롭지 않은 데이터를 반환하지 않도록 결과 집합 크기를 줄입니다. 이 기능은 초기(실패) 쿼리가 너무 "넓다"(예: 필요하지 않은 데이터 열을 투영하지 않는 경우)에 일반적으로 유용합니다.
* 쿼리 후 처리(예: 집계)를 쿼리 자체로 이동하여 결과 집합 크기를 줄입니다. 이 기능은 쿼리 출력이 다른 처리 시스템에 공급되어 추가 집계를 수행하는 시나리오에서 유용합니다. 
* 쿼리에서 [데이터 내보내기로](../management/data-export/index.md)전환합니다.
   이는 서비스에서 큰 데이터 집합을 내보내려는 경우에 적합합니다.
* 서비스에 이 쿼리 제한을 억제하도록 지시합니다.

쿼리에서 생성된 결과 집합 크기를 줄이는 일반적인 방법은 다음과 같습니다.

* [연산자](../query/summarizeoperator.md) 그룹을 요약하고 쿼리 출력에서 유사한 레코드를 집계하여 집계 [함수를](../query/any-aggfunction.md)사용하여 일부 열을 샘플링할 수 있습니다.
* 테이크 [연산자를](../query/takeoperator.md) 사용하여 쿼리 출력을 샘플링합니다.
* 하위 [문자열 함수를](../query/substringfunction.md) 사용하여 넓은 자유 텍스트 열을 트리밍합니다.
* 프로젝트 [연산자를](../query/projectoperator.md) 사용하여 결과 집합에서 흥미롭지 않은 열을 삭제합니다.

요청 옵션을 사용하여 결과 잘림을 비활성화할 `notruncation` 수 있습니다. 이 경우 어떤 형태의 제한이 여전히 제자리에 두는 것이 좋습니다. 다음은 그 예입니다.

```kusto
set notruncation;
MyTable | take 1000000
```

또한 `truncationmaxsize` 값(최대 데이터 크기(바이트 의 최대 데이터 크기, 기본값은 64MB)과(최대 `truncationmaxrecords` 레코드 수, 기본값은 500,000)을 설정하여 결과 잘림을 보다 세련된 제어할 수 있습니다. 예를 들어 다음 쿼리 집합은 1,105개의 레코드 또는 1MB 중 초과된 레코드에서 발생하는 결과 잘림을 설정합니다.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

Kusto 클라이언트 라이브러리는 현재 이 제한이 있다고 가정합니다. 경계 없이 제한을 늘릴 수 있지만, 결국 현재 구성할 수 없는 클라이언트 한도에 도달하게 됩니다. 한 가지 가능한 해결 방법은 REST API 계약에 대해 직접 프로그래밍하고 Kusto 쿼리 결과에 대한 스트리밍 파서를 구현하는 것입니다. 스트리밍 클라이언트의 우선 순위를 적절하게 지정할 수 있도록 Kusto 팀에 이 문제가 발생했는지 알려주십시오.

결과 잘림은 기본적으로 클라이언트에 반환되는 결과 스트림뿐만 아니라 적용됩니다. 또한 한 Kusto 클러스터가 유사한 효과를 가진 클러스터 간 쿼리에서 다른 Kusto 클러스터에 문제가 있는 하위 쿼리에도 기본적으로 적용됩니다.

## <a name="limit-on-memory-per-iterator"></a>이터레이터당 메모리 제한

**결과 설정 리터레이터당 최대 메모리는** Kusto가 "런어웨이" 쿼리로부터 보호하기 위해 사용하는 또 다른 제한입니다. 이 제한(요청 옵션으로 `maxmemoryconsumptionperiterator`표시)은 단일 쿼리 계획 결과 집합 ITERATOR가 보유할 수 있는 메모리 양에 대한 상한을 설정합니다. (이 제한은 .와 같이 `join`본질적으로 스트리밍되지 않는 특정 거리터에 적용됩니다.) 다음은 이 경우 반환되는 몇 가지 오류 메시지입니다.

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

기본적으로 이 값은 5GB로 설정됩니다. 이 값을 컴퓨터의 실제 메모리의 절반까지 늘릴 수 있습니다.

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

이러한 제한을 제거하는 것을 고려할 때 먼저 실제로 값을 얻을 수 있는지 확인합니다. 특히 결과 잘림 제한을 제거하면 대량 데이터를 Kusto 밖으로 이동하려는 경우(대신 `.export` 명령을 사용해야 하는 경우) 나중에 집계(이 경우 Kusto를 사용하여 집계하는 것이 좋습니다)를 사용하려는 경우
Kusto 팀에 이러한 제안된 솔루션 중 하나에서 충족할 수 없는 비즈니스 시나리오가 있는지 알려주십시오.  

대부분의 경우 데이터 집합을 10%로 샘플링하여 이 제한을 초과하지 않도록 할 수 있습니다. 아래 두 쿼리는 이 샘플링을 수행하는 방법을 보여 주며 있습니다. 첫 번째는 통계 샘플링(난수 생성기 사용)입니다. 두 번째는 결정적 샘플링입니다(데이터 집합에서 일부 열을 해시하여 일반적으로 일부 ID).

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>노드당 메모리 제한

**노드당 쿼리당 최대 메모리는** Kusto가 "런어웨이" 쿼리로부터 보호하기 위해 사용하는 또 다른 제한입니다. 이 제한(요청 옵션으로 `max_memory_consumption_per_query_per_node`표시)은 특정 쿼리에 대해 단일 노드에 할당할 수 있는 메모리 양에 대한 상한을 설정합니다. 


```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>누적 된 문자열 집합에 대한 제한

다양한 쿼리 작업에서 Kusto는 결과 생성을 시작하기 전에 문자열 값을 "수집"하고 내부적으로 버퍼링해야 합니다. 이러한 누적된 문자열 집합의 크기와 보유할 수 있는 항목 수가 제한되어 있습니다. 또한 각 개별 문자열은 특정 제한을 초과할 수 없습니다.
이러한 제한 중 하나를 초과하면 다음 오류 중 하나가 발생합니다.

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

현재 최대 문자열 집합 크기를 늘리는 스위치가 없습니다.
해결 해결 으로 버퍼링 해야 하는 데이터의 양을 줄이기 위해 쿼리를 다시 구사 합니다. 예를 들어, 조인 및 요약과 같은 연산자 "입력"하기 전에 불필요한 열을 투영하여 투영합니다. 또는, 예를 [들어, 셔플 쿼리](../query/shufflequery.md) 전략을 사용하여.

## <a name="limit-on-request-execution-time-timeout"></a>요청 실행 시간 제한(시간 제한)

**서버 시간 시간은** 모든 요청에 적용되는 서비스 측 시간 입니다.
실행 중인 요청(쿼리 및 제어 명령)에 대한 시간 시간이 여러 지점에서 적용됩니다.

* Kusto 클라이언트 라이브러리에서(사용된 경우)
* 요청을 수락하는 Kusto 서비스 끝점에서
* 요청을 처리하는 Kusto 서비스 엔진에서

기본적으로 시간 초과는 쿼리의 경우 4분, 제어 명령의 경우 10분으로 설정됩니다. 이 값은 필요한 경우(1시간 제한)을 늘릴 수 있습니다.

* Kusto.Explorer를 사용하여 쿼리하는 경우 **도구** &gt; **옵션** *  &gt; **연결 쿼리** &gt; **서버 시간 지정을**사용합니다.
* 프로그래밍 방식으로 `servertimeout` 클라이언트 요청 속성(형식 `System.TimeSpan`값, 최대 1시간 값)을 설정합니다.

시간 시간에 대한 참고 사항:

* 클라이언트 측에서는 응답이 클라이언트에 도착하기 시작할 때까지 생성되는 요청부터 시간 시간이 적용됩니다. 클라이언트에서 페이로드를 다시 읽는 데 걸리는 시간은 시간 시간의 일부로 처리되지 않습니다(호출자는 스트림에서 데이터를 가져오는 빈도에 따라 달라지기 때문입니다).
* 또한 클라이언트 측에서 사용되는 실제 시간 초과 값은 사용자가 요청한 서버 시간 초과 값보다 약간 높습니다. 이는 네트워크 대기 시간을 허용하기 위한 것입니다.
* 서비스 측면에서 모든 쿼리 연산자가 시간 시간 값을 사용하지는 않습니다.
   우리는 점차적으로 그 지원을 추가하고 있습니다.
* Kusto가 허용되는 최대 요청 시간 시간을 자동으로 사용하도록 하려면 `norequesttimeout` `true`클라이언트 요청 속성을 로 설정합니다.

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here as it should not be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>쿼리 CPU 리소스 사용량 제한

기본적으로 Kusto는 실행 중인 쿼리가 클러스터에 있는 만큼의 CPU 리소스를 사용할 수 있도록 허용하며, 두 개 이상의 쿼리가 실행되는 경우 쿼리 간에 공정한 라운드 로빈을 수행하려고 시도합니다. 대부분의 경우 임시 쿼리에 대해 최상의 성능을 얻을 수 있습니다.
다른 경우에는 특정 쿼리에 할당된 CPU 리소스를 제한할 수 있습니다. 예를 들어 "백그라운드 작업"을 실행하는 경우 동시 임시 쿼리의 우선 순위를 높이기 위해 더 높은 대기 시간을 허용할 수 있습니다.

Kusto는 쿼리, **query_fanout_threads_percent** 및 **query_fanout_nodes_percent**실행할 때 두 개의 클라이언트 [요청 속성을](../api/netfx/request-properties.md) 지정하는 지원을 지원합니다.
둘 다 최대값(100)으로 기본값인 정수이지만 특정 쿼리에 대해 일부 값으로 축소될 수 있습니다. 첫 번째는 스레드 사용에 대한 팬아웃 요소를 제어합니다. 클러스터가 100%이면 클러스터는 각 노드의 모든 CPU(예: Azure D14 노드에 배포된 클러스터, 16개의 CPU)를 쿼리에 할당하고, CPU의 절반 보다 50% 이상이 사용되는 경우 등(숫자는 전체 CPU로 반올림되므로 0으로 설정하는 것이 안전합니다). 두 번째 노드는 하위 쿼리 배포 작업당 사용할 클러스터의 노드 수와 유사한 방식으로 함수를 제어합니다.

## <a name="limit-on-query-complexity"></a>쿼리 복잡성 제한

쿼리 를 실행하는 동안 쿼리 텍스트는 쿼리를 나타내는 관계형 연산자 트리로 변환됩니다.
트리 깊이가 내부 임계값(수천 수준)을 초과하는 경우 쿼리는 처리하기에 너무 복잡하다고 간주되며 관계형 연산자 트리가 한계를 초과했음을 나타내는 오류 코드로 실패합니다.
대부분의 경우 이 문제는 다음과 같은 이진 연산자의 긴 목록이 함께 연결된 쿼리로 인해 발생합니다.

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

이 특정 경우 - [`in()`](../query/inoperator.md) 연산자사용으로 쿼리를 다시 작성하는 것이 좋습니다. 

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```