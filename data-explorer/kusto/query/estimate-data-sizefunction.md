---
title: estimate_data_size() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 estimate_data_size()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9664a7c9caf0506cdb7274eed5e143f89c82cebb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515728"
---
# <a name="estimate_data_size"></a>estimate_data_size()

테이블 형식 식의 선택한 열의 바이트 단위로 예상 데이터 크기를 반환합니다.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**구문**

`estimate_data_size(*)`

`estimate_data_size(`*콜1*`, `*col2*`, `...`)`

**인수**

* *col1*, *col2*: 데이터 크기 추정에 사용되는 소스 테이블 형식 식에서 열 참조 를 선택합니다. 모든 열을 포함하려면 `*` (별표) 구문을 사용합니다.

**반환**

* 레코드 크기의 바이트로 예상된 데이터 크기입니다. 추정은 데이터 유형 및 값 길이를 기반으로 합니다.

**예**

다음을 사용하여 `estimated_data_size()`총 데이터 크기 계산

```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|합계|
|---|
|180|