---
title: 개수 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 개수 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: c0d0286919a68b6e58065e0a6fe7e0d24b1cdd5f
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610231"
---
# <a name="count-operator"></a>count 연산자

입력 레코드 집합의 레코드 수를 반환합니다.

**구문**

`T | count`

**인수**

*T*: 레코드 수를 계산할 테이블 형식 데이터입니다.

**반환**

이 함수는 형식 `long`의 단일 레코드 및 열을 가진 테이블을 반환합니다. 유일한 셀의 값은 *T*의 레코드 수입니다. 

**예제**

```kusto
StormEvents | count
```
