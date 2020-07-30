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
ms.openlocfilehash: 4a3a1150996000742f5065df0eddc385074eaa48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348091"
---
# <a name="external_table"></a>external_table()

이름으로 외부 테이블을 참조 합니다.

```kusto
external_table('StormEvent')
```

## <a name="syntax"></a>구문

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>인수

* *TableName*: 쿼리 중인 외부 테이블의 이름입니다.
  는 종류의 외부 테이블을 참조 하는 문자열 리터럴이어야 `blob` 합니다 `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: 실제 (외부) 데이터 분할의 필드를이 함수에 의해 출력 되는 열에 매핑하는 매핑 개체의 선택적 이름입니다.

**참고**

외부 테이블에 대 한 자세한 내용은 [외부 테이블](schema-entities/externaltables.md) 을 참조 하세요.

[외부 테이블 관리 명령도](../management/externaltables.md)참조 하세요.

함수에는 `external_table` [테이블](tablefunction.md) 함수와 유사한 제한이 있습니다.