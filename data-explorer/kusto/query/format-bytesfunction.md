---
title: format_bytes ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 format_bytes ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c324813ed53b57673f8962f87374eb998f2a3443
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244761"
---
# <a name="format_bytes"></a>format_bytes()

숫자의 형식을 데이터 크기 (바이트)를 나타내는 문자열로 지정 합니다.

```kusto
format_bytes(1024) == '1 KB'"
```

## <a name="syntax"></a>구문

`format_bytes(`*값* [ `,` *전체 자릿수* [ `,` *단위*]]`)`

## <a name="arguments"></a>인수

* `value`: 데이터 크기 (바이트)로 지정할 숫자입니다.
* `precision`: (선택 사항) 값이 반올림 될 자릿수입니다. 기본값은 0입니다.
* `units`: (선택 사항) 문자열 형식에서 ( `Bytes` , `KB` , `MB` ,, `GB` `TB` , `PB` )을 사용 하는 대상 데이터 크기의 단위입니다. 매개 변수가 비어 있으면 입력 값에 따라 단위가 자동으로 선택 됩니다.

## <a name="returns"></a>반환

형식 결과를 포함 하는 문자열입니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564 바이트|10.1 KB|19 MB|19.08 M B|19541 KB|
