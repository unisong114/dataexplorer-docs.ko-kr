---
title: 패싯 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 패싯 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0e5bc062b99a97b8d11c11312aac2d5829d6584b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348023"
---
# <a name="facet-operator"></a>facet 연산자

지정 된 각 열에 대해 하나씩 테이블 집합을 반환 합니다.
각 테이블은 해당 열에서 사용 하는 값 목록을 지정 합니다.
절을 사용 하 여 추가 테이블을 만들 수 있습니다 `with` .

## <a name="syntax"></a>Syntax

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *filterpipe*`)`

## <a name="arguments"></a>인수

* *ColumnName:* 출력 테이블로 요약할 입력의 열 이름입니다.
* *Filterpipe:* 출력 중 하나를 생성 하기 위해 입력 테이블에 적용 되는 쿼리 식입니다.

## <a name="returns"></a>반환

여러 테이블: 절에 대해 하나 `with` , 각 열에 대해 하나씩

## <a name="example"></a>예제

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```