---
title: parse_version ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_version ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 57ef9eef8f4df5564adc70ed6dd965329fa1807a
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804052"
---
# <a name="parse_version"></a>parse_version()

버전의 입력 문자열 표현을 비교할 수 있는 10 진수로 변환 합니다.

```kusto
parse_version("0.0.0.1")
```

## <a name="syntax"></a>구문

`parse_version``(` *Expr*`)`

## <a name="arguments"></a>인수

* *`Expr`*: `string` 구문 분석할 버전을 지정 하는 형식의 스칼라 식입니다.

> [!NOTE]
> * 입력 문자열은 숫자로 표시 되 고 마침표 ('. ')로 구분 된 1 ~ 4 개의 버전 부분을 포함 해야 합니다.
> * 버전의 각 부분에는 최대 8 개의 숫자가 포함 될 수 있으며 최대 값은 99999999입니다.
> * 파트 수가 4 보다 작은 경우 누락 된 부분은 모두 후행 ()으로 간주 됩니다 `1.0`  ==  `1.0.0.0` .

## <a name="returns"></a>반환

성공적으로 변환 되 면 10 진수가 반환 됩니다.
변환에 실패 하면 결과는가 됩니다 `null` .

## <a name="example"></a>예제

```kusto
let dt = datatable(v:string)
["0.0.0.5","0.0.7.0","0.0.3","0.2","0.1.2.0","1.2.3.4","1","99999999.0.0.0"];
dt | project v1=v, _key=1 
| join kind=inner (dt | project v2=v, _key = 1) on _key | where v1 != v2
| summarize v1 = max(v1),v2 = min(v2) by (hash(v1) + hash(v2)) // removing duplications
| project v1, v2, higher_version = iif(parse_version(v1) > parse_version(v2), v1, v2)

```

|v1|v2|higher_version|
|---|---|---|
|99999999.0.0.0|0.0.0.5|99999999.0.0.0|
|1|0.0.0.5|1|
|1.2.3.4|0.0.0.5|1.2.3.4|
|0.1.2.0|0.0.0.5|0.1.2.0|
|0.2|0.0.0.5|0.2|
|0.0.3|0.0.0.5|0.0.3|
|0.0.7.0|0.0.0.5|0.0.7.0|
|99999999.0.0.0|0.0.7.0|99999999.0.0.0|
|1|0.0.7.0|1|
|1.2.3.4|0.0.7.0|1.2.3.4|
|0.1.2.0|0.0.7.0|0.1.2.0|
|0.2|0.0.7.0|0.2|
|0.0.7.0|0.0.3|0.0.7.0|
|99999999.0.0.0|0.0.3|99999999.0.0.0|
|1|0.0.3|1|
|1.2.3.4|0.0.3|1.2.3.4|
|0.1.2.0|0.0.3|0.1.2.0|
|0.2|0.0.3|0.2|
|99999999.0.0.0|0.2|99999999.0.0.0|
|1|0.2|1|
|1.2.3.4|0.2|1.2.3.4|
|0.2|0.1.2.0|0.2|
|99999999.0.0.0|0.1.2.0|99999999.0.0.0|
|1|0.1.2.0|1|
|1.2.3.4|0.1.2.0|1.2.3.4|
|99999999.0.0.0|1.2.3.4|99999999.0.0.0|
|1.2.3.4|1|1.2.3.4|
|99999999.0.0.0|1|99999999.0.0.0|
