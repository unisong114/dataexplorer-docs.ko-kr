---
title: take operator-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 take operator에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 569554379ffe672ed75fd15da127f03fea35f6d1
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748385"
---
# <a name="take-operator"></a>take 연산자

지정한 수 만큼의 행을 반환 합니다.

```kusto
T | take 5
```

원본 데이터가 정렬 되지 않은 경우 레코드가 반환 되는 것을 보장 하지 않습니다.

> [!NOTE]
> `take` 는 데이터 집합이 변경 되지 않은 경우에도 데이터를 대화형으로 검색할 때 작은 레코드 샘플을 볼 수 있는 간단 하 고 빠르고 효율적인 방법 이지만 여러 번 실행 하는 경우 결과의 일관성을 보장 하지는 않습니다.
> 쿼리에서 반환 되는 행 수가 쿼리에 의해 명시적으로 제한 되지 않더라도 (연산자 사용 안 함 `take` ) Kusto 기본적으로 해당 수를 제한 합니다. 자세한 내용은 [Kusto 쿼리 제한](../concepts/querylimits.md)을 참조 하세요.

## <a name="syntax"></a>Syntax

`take`*Numberofrows* 
 `limit` *Numberofrows*

`take`및 `limit` 는 동의어입니다.

## <a name="paging-of-query-results"></a>쿼리 결과의 페이징

페이징을 구현 하는 방법은 다음과 같습니다.

* 쿼리 결과를 외부 저장소로 내보내고 생성 된 데이터를 페이징 합니다.
* Kusto 쿼리 결과를 캐시 하 여 상태 저장 페이징 API를 제공 하는 중간 계층 응용 프로그램을 작성 합니다.
* [저장 된 쿼리 결과](../management/stored-query-results.md#pagination) 에서 페이지 매김을 사용 합니다.


## <a name="see-also"></a>참고 항목

* [sort 연산자](sortoperator.md)
* [top 연산자](topoperator.md)
* [top-nested 연산자](topnestedoperator.md)
