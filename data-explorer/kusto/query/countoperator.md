---
title: count 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 count 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 14895e17d77e3bbf1d98d2d68221930d3f291774
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644623"
---
# <a name="count-operator"></a>count 연산자

입력 레코드 집합의 레코드 수를 반환 합니다.

## <a name="syntax"></a>구문

`T | count`

## <a name="arguments"></a>인수

*T*: 레코드 수를 계산할 테이블 형식 데이터입니다.

## <a name="returns"></a>반환

이 함수는 형식 `long`의 단일 레코드 및 열을 가진 테이블을 반환합니다. 유일한 셀의 값은 *T* 의 레코드 수입니다. 

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>참고 항목

Count () 집계 함수에 대 한 자세한 내용은 [count () (집계 함수)](count-aggfunction.md)를 참조 하세요.
