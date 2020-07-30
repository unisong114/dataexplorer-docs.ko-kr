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
ms.openlocfilehash: 969efc142f1cd823b319a5c98494542fb2603f24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348737"
---
# <a name="count-operator"></a>count 연산자

입력 레코드 집합의 레코드 수를 반환 합니다.

## <a name="syntax"></a>구문

`T | count`

## <a name="arguments"></a>인수

*T*: 레코드 수를 계산할 테이블 형식 데이터입니다.

## <a name="returns"></a>반환

이 함수는 형식 `long`의 단일 레코드 및 열을 가진 테이블을 반환합니다. 유일한 셀의 값은 *T*의 레코드 수입니다. 

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
