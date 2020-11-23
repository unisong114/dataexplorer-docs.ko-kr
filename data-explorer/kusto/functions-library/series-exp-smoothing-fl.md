---
title: series_exp_smoothing_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_exp_smoothing_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: 373dd34145a644fe14ea4c077a8c55ddf428ba6d
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324931"
---
# <a name="series_exp_smoothing_fl"></a>series_exp_smoothing_fl ()

계열에 기본 지 수 다듬기 필터를 적용 합니다.

함수는 `series_exp_smoothing_fl()` 동적 숫자 배열을 입력으로 포함 하는 식을 사용 하 여 [기본 지 수 다듬기](https://en.wikipedia.org/wiki/Exponential_smoothing#Basic_(simple)_exponential_smoothing_(Holt_linear)) 필터를 적용 합니다.

> [!NOTE]
> 이 함수는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>구문

`series_exp_smoothing_fl(`*y_series* `, [` *알파*`])`
  
## <a name="arguments"></a>인수

* *y_series*: 숫자 값의 동적 배열 셀입니다.
* *alpha*: [0-1] 범위의 선택적 실수 값으로, 마지막 점의 무게와 이전 점의 가중치 (즉)를 지정 합니다. `1-alpha` 기본값은 0.5입니다.

## <a name="usage"></a>사용량

`series_exp_smoothing_fl()` 는 사용자 정의 함수입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_exp_smoothing_fl = (y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
;
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면 [. create 함수](../management/create-function.md)를 사용 합니다. 함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Basic exponential smoothing for a series")
series_exp_smoothing_fl(y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

---

:::image type="content" source="images/series-exp-smoothing-fl/exp-smoothing-demo.png" alt-text="인공 계열의 지 수 다듬기를 보여 주는 그래프" border="false":::
