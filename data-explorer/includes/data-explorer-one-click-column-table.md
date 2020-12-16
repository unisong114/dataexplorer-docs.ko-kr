---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 06/11/2020
ms.author: orspodek
ms.openlocfilehash: 3d366a824ecfdf89c56b1049b70df664573cd7e0
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "97371665"
---
테이블에서 변경할 수 있는 사항은 다음 매개 변수에 따라 다릅니다.
* **테이블** 유형은 신규 또는 기존입니다.
* **매핑** 유형은 신규 또는 기존입니다.

테이블 유형 | 매핑 유형 | 사용 가능한 조정|
|---|---|---|
|새 테이블   | 새 매핑 |데이터 형식 변경, 열 이름 변경, 새 열, 열 삭제, 열 업데이트, 오름차순 정렬, 내림차순 정렬  |
|기존 테이블  | 새 매핑 | 새 열(데이터 형식 변경, 이름 바꾸기 및 업데이트 가능) <br> 열 업데이트, 오름차순 정렬, 내림차순 정렬  |
| | 기존 매핑 | 오름차순 정렬, 내림차순 정렬

> [!NOTE]
> 새 열을 추가하거나 열을 업데이트하면 매핑 변환을 변경할 수 있습니다. 자세한 내용은 [매핑 변환](../ingest-data-one-click.md#mapping-transformations)을 참조하세요.