---
title: external_table ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 external_table ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: b966dbd43d1f40842240eaebf7d4008450e1f746
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343201"
---
# <a name="external_table"></a>external_table()

이름으로 [외부 테이블](schema-entities/externaltables.md) 을 참조 합니다.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * 함수에는 `external_table` [테이블](tablefunction.md) 함수와 유사한 제한이 있습니다.
> * 표준 [쿼리 제한은](../concepts/querylimits.md) 외부 테이블 쿼리에도 적용 됩니다.

## <a name="syntax"></a>구문

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>인수

* *TableName*: 쿼리 중인 외부 테이블의 이름입니다.
  는 종류의 외부 테이블을 참조 하는 문자열 리터럴이어야 `blob` 합니다 `adl` `sql` .

* *MappingName*: 실제 (외부) 데이터 분할의 필드를이 함수에 의해 출력 되는 열에 매핑하는 매핑 개체의 선택적 이름입니다.

## <a name="next-steps"></a>다음 단계

* [외부 테이블 일반 제어 명령](../management/external-table-commands.md)
* [Azure Storage 또는 Azure Data Lake의 외부 테이블 만들기 및 변경](../management/external-tables-azurestorage-azuredatalake.md)
* [외부 SQL 테이블 만들기 및 변경](../management/external-sql-tables.md)