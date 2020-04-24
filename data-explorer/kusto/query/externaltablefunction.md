---
title: external_table() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 external_table()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 9fd03fb3c8452702c3db27a5e0466e8608c04eb9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515456"
---
# <a name="external_table"></a>external_table()

이름으로 외부 테이블을 참조합니다.

```kusto
external_table('StormEvent')
```

**구문**

`external_table``(` *테이블* 이름`,` [ *매핑 이름* ]`)`

**인수**

* *테이블 이름*: 쿼리되는 외부 테이블의 이름입니다.
  문자 그대로 의 `blob` 외부 테이블을 참조하는 문자열이어야 `adl`합니다. <!-- TODO: Document data formats supported -->

* *매핑 이름*: 실제(외부) 데이터 샤드의 필드를 이 함수의 출력열에 매핑하는 매핑 개체의 선택적 이름입니다.

**참고 사항**

[외부 테이블에](schema-entities/externaltables.md) 대한 자세한 내용은 외부 테이블을 참조하십시오.

외부 [테이블 관리에 대한 명령도](../management/externaltables.md)참조하십시오.

이 `external_table` 함수에는 [테이블](tablefunction.md) 함수와 유사한 제한이 있습니다.