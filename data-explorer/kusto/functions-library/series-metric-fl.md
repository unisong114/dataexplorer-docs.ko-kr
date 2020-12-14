---
title: series_metric_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_metric_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 2af82906be2cdbffdc69646a3050376e0a1af867
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389191"
---
# <a name="series_metric_fl"></a>series_metric_fl ()


`series_metric_fl()`함수는 [프로메테우스](https://prometheus.io/) 모니터링 시스템을 사용 하 여 Azure 데이터 탐색기 수집 메트릭 시계열을 선택 하 고 검색 합니다. 이 함수는 Azure 데이터 탐색기에 저장 된 데이터가 [프로메테우스 데이터 모델](https://prometheus.io/docs/concepts/data_model/)에 따라 구조화 되어 있다고 가정 합니다. 특히 각 레코드에는 다음이 포함 됩니다.
 * timestamp 
 * 메트릭 이름 
 * 메트릭 값 
 * 레이블 (쌍)의 변수 집합 `key:value`
 
 프로메테우스는 메트릭 이름과 개별 레이블 집합으로 시계열을 정의 합니다. 메트릭 이름과 시계열 선택기 (레이블 집합)를 지정 하 여 [PromQL (프로메테우스 Query Language](https://prometheus.io/docs/prometheus/latest/querying/basics/) )를 사용 하 여 시계열 집합을 검색할 수 있습니다.

> [!NOTE]
> `series_metric_fl()` 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>구문

`T | invoke series_metric_fl(`*timestamp_col* `,` *name_col* `,` *labels_col* `,` *value_col* `,` *metric_name* `,` *labels_selector* `,` *lookback* `,` *오프셋*`)`

## <a name="arguments"></a>인수

* *timestamp_col*: 타임 스탬프를 포함 하는 열의 이름입니다.
* *name_col*: 메트릭 이름을 포함 하는 열의 이름입니다.
* *labels_col*: 레이블 사전이 포함 된 열의 이름입니다.
* *value_col*: 메트릭 값을 포함 하는 열의 이름입니다.
* *metric_name*: 검색할 메트릭 시계열입니다.
* *labels_selector*: [PromQL와 비슷한](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors)시계열 선택기 문자열입니다. 예를 들어 쌍의 목록을 포함 하는 문자열 `key:value` `'key1:val1,key2:val2'` 입니다. 이 매개 변수는 선택 사항이 며 기본값은 빈 문자열로, 필터링 하지 않음을 의미 합니다. 정규식은 지원 되지 않습니다. 
* *lookback*: 검색할 Range vector로, [PromQL와 유사](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors)합니다. 이 매개 변수는 선택 사항이 며 기본값은 10 분입니다.
* *offset*: [PromQL와 마찬가지로](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier)현재 시간에서 검색으로 다시 오프셋 합니다. 이전 *(offset)-lookback* 에서 *전 (오프셋)* 까지 데이터를 검색 합니다. 이 매개 변수는 선택 사항이 며 기본값은 0입니다. 즉, 데이터가 지금 검색 됩니다 ().

## <a name="usage"></a>사용량

`series_metric_fl()`[호출 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 사용자 정의 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function)입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면을 사용 [`.create function`](../management/create-function.md) 합니다. 함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="10 분 동안 디스크 쓰기 메트릭을 보여 주는 그래프" border="false":::

## <a name="example"></a>예

다음 예제에서는 선택기를 지정 하지 않으므로 모든 ' 쓰기 ' 메트릭이 선택 됩니다. 이 예에서는 함수가 이미 설치 되어 있다고 가정 하 고 입력 테이블을 첫 번째 매개 변수로 지정 하 여 대체 직접 호출 구문을 사용 합니다.
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="10 분 동안 모든 디스크에 대 한 디스크 쓰기 메트릭을 보여 주는 그래프" border="false":::
