---
title: count 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 count 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 9a34734ebfee94646b2b2f15730f14f9d2709c6d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227515"
---
# <a name="count-operator"></a>count 연산자

입력 레코드 집합의 레코드 수를 반환 합니다.

**구문**

`T | count`

**인수**

*T*: 레코드 수를 계산할 테이블 형식 데이터입니다.

**반환**

이 함수는 형식 `long`의 단일 레코드 및 열을 가진 테이블을 반환합니다. 유일한 셀의 값은 *T*의 레코드 수입니다. 

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
