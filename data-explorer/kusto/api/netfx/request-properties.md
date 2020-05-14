---
title: Kusto Request 속성 & ClientRequestProperties-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 요청 속성 ClientRequestProperties에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 52b1f73e539e57f82d3ec911a3ea69dcd6848e4a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382168"
---
# <a name="request-properties-clientrequestproperties"></a>요청 속성, ClientRequestProperties

.NET SDK를 통해 Kusto에서 요청 하는 경우 일반적으로 다음 값을 제공 합니다.

1. 연결할 서비스 끝점, 인증 매개 변수 및 이와 유사한 연결 관련 정보를 나타내는 연결 문자열입니다. 프로그래밍 방식으로 연결 문자열은 클래스를 통해 표현 됩니다 `KustoConnectionStringBuilder` .

2. 요청의 "범위"를 설명 하는 데 사용 되는 데이터베이스의 이름입니다.

3. 요청 (쿼리 또는 명령)의 텍스트입니다.

4. 클라이언트에서 서비스를 제공 하 고 요청에 적용 하는 추가 속성입니다. 프로그래밍 방식으로 이러한 속성은 라는 클래스에 의해 보유 됩니다 `ClientRequestProperties` .

클라이언트 요청 속성은 여러 가지 용도로 사용 됩니다. 이러한 속성 중 일부는 클라이언트/서비스 상호 작용을 추적 하는 데 사용할 수 있는 상관 관계 문자열을 제공 하는 등의 작업을 더 쉽게 수행 하는 데 사용 되 고, 다른 일부는 요청에 적용 되는 제한 및 정책에 영향을 주는 데 사용 되며, 이러한 속성의 세 번째 범주는 [쿼리 매개 변수](../../query/queryparametersstatement.md)입니다. 이 페이지에는 지원 되는 속성의 전체 목록이 표시 됩니다.

`Kusto.Data.Common.ClientRequestProperties`클래스는 세 가지 종류의 데이터를 포함 합니다.

* 명명 된 속성.

* 옵션 (옵션 이름을 옵션 값으로 매핑).

* 매개 변수 (쿼리 매개 변수 이름을 쿼리 매개 변수 값으로 매핑).

> [!NOTE]
> 일부 명명 된 속성은 "사용 안 함"으로 표시 됩니다. 이러한 속성은 클라이언트에서 지정 하면 안 되며 서비스에는 영향을 주지 않습니다.

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>ClientRequestId (x-y-요청 id) 명명 된 속성

이 명명 된 속성은 클라이언트에서 지정한 요청 id를 보유 합니다. 클라이언트에서 전송 하는 각 요청에 대해 고유한 요청당 값을 지정 하는 것이 좋습니다 (예: 쿼리 취소 등의 몇 가지 시나리오에서 필요 함).

이 속성의 프로그래밍 이름은 이며 `ClientRequestId` HTTP 헤더로 변환 됩니다 `x-ms-client-request-id` .

클라이언트에서 고유한 값을 지정 하지 않으면 SDK에서이 속성을 (임의) 값으로 설정 합니다.

이 속성의 내용에는 GUID와 같은 인쇄 가능한 모든 고유 문자열이 있을 수 있습니다.
그러나 클라이언트에서 다음 템플릿을 사용 하는 것이 좋습니다.

*ApplicationName* `.` *ActivityName* `;` *UniqueId*

여기서 *ApplicationName* 은 요청 하는 클라이언트 응용 프로그램을 식별 하 고, *ActivityName* 는 클라이언트 응용 프로그램에서 클라이언트 요청을 발급 하는 작업의 종류를 식별 하 고, *UniqueId* 는 특정 요청을 식별 합니다.

## <a name="the-application-x-ms-app-named-property"></a>응용 프로그램 (x-y) 명명 된 속성

이 명명 된 속성은 추적을 위해 요청을 수행 하는 클라이언트 응용 프로그램의 이름을 보유 합니다.

이 속성의 프로그래밍 이름은 이며 `Application` HTTP 헤더로 변환 됩니다 `x-ms-app` . Kusto 연결 문자열에으로 지정할 수 있습니다 `Application Name for Tracing` .

클라이언트에서 고유한 값을 지정 하지 않는 경우이 속성은 SDK를 호스트 하는 프로세스의 이름으로 설정 됩니다.

## <a name="the-user-x-ms-user-named-property"></a>사용자 (x-y 사용자) 명명 된 속성

이 명명 된 속성은 추적을 위해 요청을 수행 하는 사용자의 id를 보유 합니다.

이 속성의 프로그래밍 이름은 이며 `User` HTTP 헤더로 변환 됩니다 `x-ms-user` . Kusto 연결 문자열에으로 지정할 수 있습니다 `User Name for Tracing` .

## <a name="controlling-request-properties-using-the-rest-api"></a>REST API를 사용 하 여 요청 속성 제어

Kusto 서비스에 대 한 HTTP 요청을 실행 하는 경우 `properties` 요청 속성을 제공 하는 POST 요청 본문 인 JSON 문서에서 슬롯을 사용 합니다. 클라이언트에서 요청을 식별 하기 위해 서비스에 제공 하는 상관 관계 ID 인 "클라이언트 요청 ID"와 같은 일부 속성은 HTTP 헤더에 제공 될 수 있으므로 HTTP GET을 사용 하는 경우에도 설정할 수 있습니다.
추가 정보 [는 Kusto REST API request 개체를](../rest/request.md) 참조 하세요.

## <a name="providing-values-for-query-parameterization-as-request-properties"></a>쿼리 매개 변수화에 대 한 값을 요청 속성으로 제공

Kusto 쿼리는 쿼리 텍스트에서 특수 한 [declare 쿼리 매개 변수](../../query/queryparametersstatement.md) 문을 사용 하 여 쿼리 매개 변수를 참조할 수 있습니다. 이렇게 하면 클라이언트 응용 프로그램에서 사용자 입력을 기반으로 하 여 쿼리를 안전 하 게 매개 변수화 할 수 있습니다 (삽입 공격을 걱정 하지 않음).

프로그래밍 방식으로, 및 메서드를 사용 하 여 속성 값을 설정할 수 있습니다 `ClearParameter` `SetParameter` `HasParameter` .

REST API에서 쿼리 매개 변수는 다른 요청 속성과 동일한 JSON 인코딩 문자열에 표시 됩니다.

## <a name="sample-code-for-using-request-properties"></a>요청 속성 사용에 대 한 샘플 코드

클라이언트 코드의 예제는 다음과 같습니다.

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>ClientRequestProperties 목록

<!-- The following is auto-generated by running the following command: -->
<!-- Kusto.Cli.exe -execute:"#crp -doc"                                -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `debug_query_externaldata_projection_fusion_disabled`(*OptionDebugQueryDisableExternalDataProjectionFusion*): 설정 하는 경우 ExternalData 연산자로 퓨즈 프로젝션을 하지 않습니다. 부울
* `debug_query_fanout_threads_percent_external_data`(*OptionDebugQueryFanoutThreadsPercentExternalData*): 외부 데이터 노드에 대해 실행을 fanout 스레드 비율입니다. Int
* `deferpartialqueryfailures`(*OptionDeferPartialQueryFailures*): true 이면 결과 집합의 일부로 부분 쿼리 오류 보고를 사용 하지 않도록 설정 합니다. 부울
* `max_memory_consumption_per_query_per_node`(*OptionMaxMemoryConsumptionPerQueryPerNode*): 전체 쿼리가 노드당 할당할 수 있는 기본 최대 메모리 크기를 재정의 합니다. 작아
* `maxmemoryconsumptionperiterator`(*OptionMaxMemoryConsumptionPerIterator*): 쿼리 연산자가 할당할 수 있는 기본 최대 메모리 양을 재정의 합니다. 작아
* `maxoutputcolumns`(*Optionmaxoutputcolumns*): 쿼리에서 생성할 수 있는 기본 최대 열 수를 재정의 합니다. 시간과
* `norequesttimeout`(*OptionNoRequestTimeout*): 요청 시간 제한을 최대 값으로 설정할 수 있습니다. 부울
* `notruncation`(:*No잘림*): 호출자에 게 반환 되는 쿼리 결과를 무시 하도록 설정 합니다. 부울
* `push_selection_through_aggregation`(*OptionPushSelectionThroughAggregation*): True 인 경우 집계를 통해 단순 선택 항목 푸시 [Boolean]
* `query_admin_super_slacker_mode`(*OptionAdminSuperSlackerMode*): True 인 경우 쿼리 실행을 다른 노드에 위임 [Boolean]
* `query_bin_auto_at`(*Querybinautoat*): bin_auto () 함수를 계산할 때 사용할 시작 값입니다. [LiteralExpression]
* `query_bin_auto_size`(*Querybinautosize*): bin_auto () 함수를 계산할 때 사용할 bin 크기 값입니다. [LiteralExpression]
* `query_cursor_after_default`(*OptionQueryCursorAfterDefault*): 매개 변수 없이 호출 되는 경우 cursor_after () 함수의 기본 매개 변수 값입니다. [string]
* `query_cursor_before_or_at_default`(*OptionQueryCursorBeforeOrAtDefault*): 매개 변수 없이 호출 되는 경우 cursor_before_or_at () 함수의 기본 매개 변수 값입니다. [string]
* `query_cursor_current`(*OptionQueryCursorCurrent*): cursor_current () 또는 current_cursor () 함수에서 반환 된 커서 값을 재정의 합니다. [string]
* `query_cursor_scoped_tables`(*OptionQueryCursorScopedTables*): cursor_after_default 범위를 지정 해야 하는 테이블 이름 목록입니다. cursor_before_or_at_default (상한은 선택 사항)입니다. 비동적
* `query_datascope`(*)*: 쿼리가 모든 데이터에 적용 되는지 또는 그 일부에만 적용 되는지 여부를 제어 합니다. [' default ', ' all ' 또는 ' hotcache ']
* `query_datetimescope_column`(*OptionQueryDateTimeScopeColumn*): 쿼리의 날짜/시간 범위 (query_datetimescope_to/query_datetimescope_from)에 대 한 열 이름을 제어 합니다. 문자열
* `query_datetimescope_from`(*OptionQueryDateTimeScopeFrom*): 쿼리의 datetime 범위 (가장 이른)를 제어 합니다. (정의 된 경우) query_datetimescope_column에만 자동 적용 된 필터로 사용 됩니다 (정의 된 경우). [DateTime]
* `query_datetimescope_to`(*OptionQueryDateTimeScopeTo*): 쿼리의 datetime 범위 (최신)를 제어 합니다. (정의 된 경우) query_datetimescope_column에만 자동 적용 된 필터로 사용 됩니다 (정의 된 경우). [DateTime]
* `query_distribution_nodes_span`(*OptionQueryDistributionNodesSpanSize*): 설정 된 경우 하위 쿼리 병합이 동작 하는 방식을 제어 합니다. 실행 노드는 노드의 각 하위 그룹에 대 한 쿼리 계층 구조에 추가 수준을 제공 합니다. 하위 그룹의 크기는이 옵션으로 설정 됩니다. Int
* `query_fanout_nodes_percent`(*OptionQueryFanoutNodesPercent*): 실행을 fanout 노드의 백분율입니다. Int
* `query_fanout_threads_percent`(*OptionQueryFanoutThreadsPercent*): 실행을 fanout 스레드 비율입니다. Int
* `query_language`(*OptionQueryLanguage*): 쿼리 텍스트를 해석 하는 방법을 제어 합니다. [' csl ', ' kql ' 또는 ' sql ']
* `query_max_entities_in_union`(*)*: 쿼리에서 생성할 수 있는 기본 최대 열 수를 재정의 합니다. 시간과
* `query_now`(*Optionquerynow*): now (0) 함수에서 반환 된 datetime 값을 재정의 합니다. [DateTime]
* `query_python_debug`(설정*Debugpython*): 설정 된 경우 열거 된 python 노드에 대해 python 디버그 쿼리를 생성 합니다 (기본값 먼저). [Boolean 또는 Int]
* `query_results_apply_getschema`(*OptionQueryResultsApplyGetSchema*): 설정 된 경우 데이터 자체가 아니라 쿼리 결과에 있는 각 테이블 형식 데이터의 스키마를 검색 합니다. 부울
* `query_results_cache_max_age`(*OptionQueryResultsCacheMaxAge*): 양수 이면 Kusto에서 반환 될 수 있는 캐시 된 쿼리 결과의 최대 보존 기간을 제어 합니다. [TimeSpan]
* `query_results_progressive_row_count`(*OptionProgressiveQueryMinRowCountPerUpdate*): 각 업데이트에서 보낼 레코드 수에 대 한 Kusto의 힌트 (OptionResultsProgressiveEnabled가 설정 된 경우에만 적용 됨)
* `query_results_progressive_update_period`(*OptionProgressiveProgressReportPeriod*): 진행률 프레임을 전송 하는 빈도에 대 한 kusto의 힌트 (OptionResultsProgressiveEnabled가 설정 된 경우에만 적용 됨)
* `query_shuffle_broadcast_join`(*ShuffleBroadcastJoin*): 브로드캐스트 조인을 통해 순서 섞기를 사용 하도록 설정 합니다.
* `query_take_max_records`(*OptionTakeMaxRecords*): 쿼리 결과를이 수의 레코드로 제한할 수 있습니다. 시간과
* `queryconsistency`(:*Query일관성*): 쿼리 일관성을 제어 합니다. [' strongconsistency ' 또는 ' normalconsistency ' 또는 ' weakconsistency ']
* `request_callout_disabled`(*;):* 지정 된 경우 요청이 사용자 제공 서비스로 호출 될 수 없음을 나타냅니다. 부울
* `request_description`(추가 요청*설명*): 요청 작성자가 요청 설명으로 포함 하려는 임의의 텍스트입니다. 문자열
* `request_external_table_disabled`(*;):* 지정 된 경우 요청이 externaltable의 코드를 호출할 수 없음을 나타냅니다. 부울
* `request_readonly`(*;):* 지정 된 경우 요청에서 아무것도 쓸 수 없어야 함을 나타냅니다. 부울
* `request_remote_entities_disabled`(*지정*된 경우): 지정 된 경우 요청이 원격 데이터베이스 및 클러스터에 액세스할 수 없음을 나타냅니다. 부울
* `request_sandboxed_execution_disabled`(*OptionRequestSandboxedExecutionDisabled*): 지정 된 경우 요청에서 샌드박스에서 코드를 호출할 수 없음을 나타냅니다. 부울
* `results_progressive_enabled`(*OptionResultsProgressiveEnabled*): 설정 된 경우 프로그레시브 쿼리 스트림을 사용 하도록 설정 합니다.
* `servertimeout`(*Optionservertimeout*): 기본 요청 시간 제한을 재정의 합니다. TimeSpan
* `truncationmaxrecords`(*OptionTruncationMaxRecords*): 쿼리가 호출자에 게 반환할 수 있는 최대 레코드 수 (잘림)를 재정의 합니다. 시간과
* `truncationmaxsize`(*OptionTruncationMaxSize*): 쿼리가 호출자에 게 반환할 수 있는 기본 최대 데이터 크기 (잘림)를 재정의 합니다. 시간과
* `validate_permissions`(:*Validatepermissions*): 쿼리를 수행할 수 있는 사용자 권한의 유효성을 검사 하 고 쿼리 자체를 실행 하지 않습니다. 부울

