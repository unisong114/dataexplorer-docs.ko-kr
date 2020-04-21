---
title: 속성 요청, ClientRequestProperties - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 요청 속성, ClientRequestProperties에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 0aa496e6011fce98db5a304b9748f27f07590b4f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524330"
---
# <a name="request-properties-clientrequestproperties"></a>속성 요청, 클라이언트 요청 속성

.NET SDK를 통해 Kusto에서 요청을 할 때 일반적으로 다음 값을 제공합니다.

1. 연결할 서비스 끝점, 인증 매개 변수 및 이와 유사한 연결 관련 정보를 나타내는 연결 문자열입니다. 프로그래밍 방식으로 연결 문자열은 클래스를 `KustoConnectionStringBuilder` 통해 표시됩니다.

2. 요청의 "범위"를 설명하는 데 사용되는 데이터베이스의 이름입니다.

3. 요청(쿼리 또는 명령) 자체의 텍스트입니다.

4. 클라이언트가 서비스를 제공하고 요청에 적용되는 추가 속성입니다. 프로그래밍 방식으로 이러한 속성은 라는 `ClientRequestProperties`클래스에 의해 유지됩니다.

클라이언트 요청 속성에는 여러 가지 용도가 있습니다. 그 중 일부는 디버깅을 더 쉽게 만드는 데 사용됩니다 (예 : 클라이언트 / 서비스 상호 작용을 추적하는 데 사용할 수있는 상관 관계 문자열을 제공함으로써), 다른 하나는 요청에 적용되는 제한 및 정책에 영향을 주는 데 사용되며 이러한 속성의 세 번째 범주는 [쿼리 매개 변수입니다.](../../query/queryparametersstatement.md) 지원되는 속성의 전체 목록이 이 페이지에 나타납니다.

클래스는 `Kusto.Data.Common.ClientRequestProperties` 세 가지 종류의 데이터를 보유합니다.

* 명명된 속성입니다.

* 옵션(옵션 이름에 대한 매핑).

* 매개 변수(쿼리 매개 변수 이름을 쿼리 매개 변수 값에 매핑)

> [!NOTE]
> 일부 명명된 속성은 "사용하지 않음"으로 표시됩니다. 이러한 속성은 클라이언트에서 지정해서는 안 되며 서비스에 영향을 주지 않습니다.

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>클라이언트RequestId (x-ms-클라이언트-요청-id) 명명 된 속성

이 명명된 속성은 요청의 클라이언트 지정 ID를 보유합니다. 디버깅 실패를 쉽게 만들고 쿼리 취소와 같은 일부 시나리오에서는 필요하므로 각 요청을 보낼 때마다 클라이언트가 요청당 고유한 값을 지정하는 것이 좋습니다.

이 속성의 프로그래밍 이름입니다. `ClientRequestId` `x-ms-client-request-id`

클라이언트가 자체 값을 지정하지 않으면 이 속성은 SDK에서 (임의) 값으로 설정됩니다.

이 속성의 내용은 GUID와 같이 인쇄가능한 고유한 문자열일 수 있습니다.
그러나 클라이언트는 다음 템플릿을 사용하는 것이 좋습니다.

*응용 프로그램 이름* `.` *활동 이름* `;` *고유 ID*

*ApplicationName이* 요청을 하는 클라이언트 응용 프로그램을 식별하는 경우 *ActivityName은* 클라이언트 응용 프로그램이 클라이언트 요청을 발행하는 활동 종류를 식별하고 *UniqueId는* 특정 요청을 식별합니다.

## <a name="the-application-x-ms-app-named-property"></a>응용 프로그램(x-ms-app) 명명된 속성

이 명명된 속성은 추적을 위해 요청을 하는 클라이언트 응용 프로그램의 이름을 보유합니다.

이 속성의 프로그래밍 이름입니다. `Application` `x-ms-app` Kusto 연결 문자열에 로 `Application Name for Tracing`지정할 수 있습니다.

클라이언트가 자체 값을 지정하지 않은 경우 이 속성은 SDK를 호스팅하는 프로세스의 이름으로 설정됩니다.

## <a name="the-user-x-ms-user-named-property"></a>사용자(x-ms-user)라는 속성

이 명명된 속성은 추적을 위해 요청을 하는 사용자의 ID를 보유합니다.

이 속성의 프로그래밍 이름입니다. `User` `x-ms-user` Kusto 연결 문자열에 로 `User Name for Tracing`지정할 수 있습니다.

## <a name="controlling-request-properties-using-the-rest-api"></a>REST API를 사용하여 요청 속성 제어

Kusto 서비스에 HTTP 요청을 발급할 때 `properties` POST 요청 기관인 JSON 문서의 슬롯을 사용하여 요청 속성을 제공합니다. 일부 속성(예: 클라이언트가 요청을 식별하기 위해 서비스에 제공하는 상관 관계 ID인 "클라이언트 요청 ID")을 HTTP 헤더에 제공할 수 있으므로 HTTP GET을 사용하는 경우에도 설정할 수 있습니다.
추가 정보는 [Kusto REST API 요청 개체를](../rest/request.md) 참조하십시오.

## <a name="providing-values-for-query-parametrization-as-request-properties"></a>요청 속성으로 쿼리 매개 변수화에 대한 값 제공

Kusto 쿼리는 쿼리 텍스트에 특수 선언 쿼리 매개 변수 문을 사용하여 쿼리 [매개 변수를](../../query/queryparametersstatement.md) 참조할 수 있습니다. 이를 통해 클라이언트 응용 프로그램은 주입 공격의 두려움 없이 안전한 방식으로 사용자 입력을 기반으로 Kusto 쿼리를 매개 변수화할 수 있습니다.

프로그래밍 방식으로 `ClearParameter`는 " `SetParameter`및 `HasParameter` 메서드를 사용하여 속성 값을 설정할 수 있습니다.

REST API에서 쿼리 매개 변수는 다른 요청 속성과 동일한 JSON 인코딩 된 문자열에 나타납니다.

## <a name="sample-code-for-using-request-properties"></a>요청 속성 사용에 대한 샘플 코드

다음은 클라이언트 코드의 예입니다.

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

## <a name="list-of-clientrequestproperties"></a>클라이언트요청속성 목록

<!-- The following is auto-generated by running the following command: -->
<!-- Kusto.Cli.exe -execute:"#crp -doc"                                -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `debug_query_externaldata_projection_fusion_disabled`*(OptionDebugQueryDisableExternalDataProjectionFusion)*: 설정된 경우 외부 데이터 연산자로 프로젝션을 융합하지 마십시오. [부울]
* `debug_query_fanout_threads_percent_external_data`*(OptionDebugQueryFanoutThreadsPercentExternalData):* 외부 데이터 노드에 대한 팬아웃 실행에 스레드의 백분율입니다. [Int]
* `deferpartialqueryfailures`*(OptionDeferPartialQueryfailures):* true이면 결과 집합의 일부로 부분 쿼리 오류를 보고하지 않도록 설정합니다. [부울]
* `max_memory_consumption_per_query_per_node`*(OptionMaxMemory소비퍼쿼리퍼노드):* 전체 쿼리가 노드당 할당할 수 있는 기본 최대 메모리 양을 재정의합니다. [UInt64]
* `maxmemoryconsumptionperiterator`*(OptionMaxMemoryPerIterator):* 쿼리 연산자가 할당할 수 있는 기본 최대 메모리 양을 재정의합니다. [UInt64]
* `maxoutputcolumns`*(OptionMaxOutputColumns):* 쿼리가 생성할 수 있는 기본 최대 열 수를 재정의합니다. 【 롱 】
* `norequesttimeout`*(OptionNoRequestTimeout):* 요청 시간 시간을 최대값으로 설정할 수 있습니다. [부울]
* `notruncation`*(OptionNoTruncation):* 호출자에게 반환된 쿼리 결과의 잘림을 억제할 수 있습니다. [부울]
* `push_selection_through_aggregation`(옵션*푸시선택ThroughAggregation):* 만약 사실이라면, 집계를 통해 간단한 선택을 밀어 [부울]
* `query_admin_super_slacker_mode`*(OptionAdminSuperSlackerMode):* true이면 쿼리 실행을 다른 노드로 위임합니다[부울]
* `query_bin_auto_at`*(QueryBinAutoAt):* bin_auto() 함수를 평가할 때 사용할 시작 값입니다. [리터럴 표현]
* `query_bin_auto_size`*(QueryBinAutoSize):* bin_auto() 함수를 평가할 때 사용할 저장소 크기 값입니다. [리터럴 표현]
* `query_cursor_after_default`*(OptionQueryCursorAfterDefault):* 매개 변수 없이 호출될 때 cursor_after() 함수의 기본 매개 변수 값입니다. [string]
* `query_cursor_before_or_at_default`*(OptionQueryCursorBeforeOrAtDefault):* 매개 변수 없이 호출될 때 cursor_before_or_at() 함수의 기본 매개 변수 값입니다. [string]
* `query_cursor_current`*(OptionQueryCursorCurrent):* cursor_current() 또는 current_cursor() 함수에서 반환되는 커서 값을 재정의합니다. [string]
* `query_cursor_scoped_tables`*(OptionQueryCursorScopedTable):* cursor_after_default 위해 범위를 지정해야 하는 테이블 이름 목록입니다. cursor_before_or_at_default(상한은 선택 사항). [다이내믹]
* `query_datascope`*(OptionQueryDataScope):* 쿼리가 모든 데이터에 적용되는지 아니면 쿼리의 일부에 만 적용되는지 여부를 쿼리의 데이터 스코프를 제어합니다. ['기본값', '모두', 또는 '핫캐시']
* `query_datetimescope_column`*(OptionQueryDateTimeScopeColumn):* 쿼리의 날짜 시간 범위(query_datetimescope_to/query_datetimescope_from)에 대한 열 이름을 제어합니다. 【 스트링 】
* `query_datetimescope_from`*(OptionQueryDateTimeScopeFrom):* 쿼리의 날짜 시간 범위(가장 빠른)query_datetimescope_column 를 제어합니다. [DateTime]
* `query_datetimescope_to`*(OptionQueryDateTimeScopeTo):* 쿼리의 날짜 시간 범위(최신)query_datetimescope_column 를 제어합니다. [DateTime]
* `query_distribution_nodes_span`*(OptionQueryDistributionNodesSpanSize):* 설정된 경우 하위 쿼리 병합이 실행되는 방식을 제어합니다. 하위 그룹의 크기는 이 옵션에 의해 설정됩니다. [Int]
* `query_fanout_nodes_percent`*(OptionQueryFanoutNodesPercent):* 팬아웃 실행에 대한 노드의 백분율입니다. [Int]
* `query_fanout_threads_percent`*(OptionQueryFanoutThreadsPercent):* 팬아웃 실행에 대한 스레드의 백분율입니다. [Int]
* `query_language`*(OptionQueryLanguage):* 쿼리 텍스트를 해석하는 방법을 제어합니다. ['csl', 'kql' 또는 'sql']
* `query_max_entities_in_union`*(OptionMaxEntitiesToUnion):* 쿼리가 생성할 수 있는 기본 최대 열 수를 재정의합니다. 【 롱 】
* `query_now`*(OptionQueryNow):* 지금(0s) 함수에서 반환되는 날짜 시간 값을 재정의합니다. [DateTime]
* `query_python_debug`*(OptionDebugPython:* 설정된 경우, 수반된 파이썬 노드(기본값 첫 번째)에 대한 파이썬 디버그 쿼리를 생성합니다. [부울 또는 이트]
* `query_results_apply_getschema`*(OptionQueryResultsApplyGetSchema):* 설정된 경우 데이터 자체 대신 쿼리 결과에서 각 테이블 형식 데이터의 스키마를 검색합니다. [부울]
* `query_results_cache_max_age`*(OptionQueryResultsCacheMaxAge):* 양수인 경우 Kusto가 반환할 수 있는 캐시된 쿼리 결과의 최대 나이를 제어합니다[TimeSpan]
* `query_results_progressive_row_count`(옵션*프로그레시브쿼리MinRowCountPerUpdate)*: 각 업데이트에서 보낼 레코드 수에 대한 쿠스토에 대한 힌트 (OptionResultsProgressiveEnabled가 설정된 경우에만 적용됨)
* `query_results_progressive_update_period`*(옵션프로그레시브프로그레시브보고서기간):* 진행률 프레임을 보내는 빈도에 대한 쿠스토에 대한 힌트(OptionResultsProgressiveEnabled가 설정된 경우에만 적용됨)
* `query_shuffle_broadcast_join`*(셔플브로드조인)*: 브로드캐스트 조인을 통해 셔플을 활성화합니다.
* `query_take_max_records`*(OptionTakeMaxRecords):* 쿼리 결과를 이 레코드 수로 제한할 수 있습니다. 【 롱 】
* `queryconsistency`*(OptionQueryConsistency):* 쿼리 일관성을 제어합니다. ['강한 일관성' 또는 '정상 일관성' 또는 '약한 불일치']
* `request_callout_disabled`*(OptionRequestCallout사용안됨:* 지정하면 요청이 사용자 제공 서비스에 호출할 수 없음을 나타냅니다. [부울]
* `request_description`*(OptionRequestDescription):* 요청 작성자가 요청 설명으로 포함하려는 임의의 텍스트입니다. 【 스트링 】
* `request_external_table_disabled`*(OptionRequestExternalTable사용 가능*없음): 지정하면 요청이 외부 테이블에서 코드를 호출할 수 없음을 나타냅니다. [부울]
* `request_readonly`*(OptionRequestReadOnly):* 지정하면 요청이 아무 것도 작성할 수 없어야 함을 나타냅니다. [부울]
* `request_remote_entities_disabled`*(OptionRequestRemoteEntities사용 안 함)*: 지정하면 요청이 원격 데이터베이스 및 클러스터에 액세스할 수 없음을 나타냅니다. [부울]
* `request_sandboxed_execution_disabled`*(OptionRequestSandboxed실행가능가능)*: 지정하면 요청이 샌드박스에서 코드를 호출할 수 없음을 나타냅니다. [부울]
* `results_progressive_enabled`*(옵션결과프로그레시브지원):* 설정된 경우 프로그레시브 쿼리 스트림을 활성화합니다.
* `servertimeout`(옵션*서버 시간 초과):* 기본 요청 시간 초과를 재정의합니다. [시간 범위]
* `truncationmaxrecords`*(OptionTruncationMaxRecords):* 쿼리가 호출자(잘림)로 돌아갈 수 있는 레코드의 기본 최대 수를 재정의합니다. 【 롱 】
* `truncationmaxsize`*(OptionTruncationMaxSize):* 쿼리가 호출자(잘림)로 돌아갈 수 있는 dfefault 최대 데이터 크기를 재정의합니다. 【 롱 】
* `validate_permissions`*(OptionValidate권한):* 쿼리를 수행할 수 있는 사용자의 사용 권한을 확인하고 쿼리 자체를 실행하지 않습니다. [부울]

