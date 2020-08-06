---
title: external_table ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 external_table ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 13b244eb151d140e3626412188ac9bc9de242cc6
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802981"
---
# <a name="external_table"></a>external_table()

이름으로 외부 테이블을 참조 합니다.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * 함수에는 `external_table` [테이블](tablefunction.md) 함수와 유사한 제한이 있습니다.
> * [외부 테이블](schema-entities/externaltables.md)
> * [외부 테이블을 관리 하기 위한 명령](../management/externaltables.md)

## <a name="syntax"></a>구문

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>인수

* *TableName*: 쿼리 중인 외부 테이블의 이름입니다.
  는 종류의 외부 테이블을 참조 하는 문자열 리터럴이어야 `blob` 합니다 `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: 실제 (외부) 데이터 분할의 필드를이 함수에 의해 출력 되는 열에 매핑하는 매핑 개체의 선택적 이름입니다.
