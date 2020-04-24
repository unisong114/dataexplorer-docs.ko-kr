---
title: 열 이름 바꾸기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 이름 변경 열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 02e692e01bb8eb0abd49c9673b000b722734db90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520505"
---
# <a name="rename-column"></a>열 이름 바꾸기

기존 테이블 열의 이름을 변경합니다.
여러 열의 이름을 변경하려면 [아래를](#rename-columns)참조하십시오.

**구문**

`.rename``column` [*데이터베이스 이름]* `.` *테이블 이름* `.` *열기존 이름* `to` *열NewName*

*여기서 DatabaseName,* *TableName,* *ColumnExistingName*및 *ColumnNewName은* 각 엔터티의 이름이며 [식별자 명명 규칙을](../query/schema-entities/entity-names.md)따릅니다.

## <a name="rename-columns"></a>열 이름 바꾸기

동일한 테이블에 있는 여러 기존 열의 이름을 변경합니다.

**구문**

`.rename``columns` *Col1* `=` [*데이터베이스 이름* `.` [ 테이블 `,` *이름* `.` *콜2]...*

이 명령을 사용하여 두 열의 이름을 바꿀 수 있습니다(각각 다른 열의 이름으로 이름이 바뀝니다.)