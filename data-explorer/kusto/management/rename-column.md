---
title: 열 이름 바꾸기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 이름 바꾸기 열에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25d0ff106d406c59c24d26542a8dad1e4992e311
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967386"
---
# <a name="rename-column"></a>. 열 이름 바꾸기

기존 테이블 열의 이름을 변경 합니다.
여러 열의 이름을 변경 하려면 [아래](#rename-columns)를 참조 하십시오.

**구문**

`.rename``column`[*DatabaseName* `.` ] *TableName* `.` *columnexistingexistingname* `to` *columnnewname*

여기서 *DatabaseName*, *TableName*, *Columnexistingname*및 *columnnewname* 은 해당 엔터티의 이름이 고 [식별자 명명 규칙](../query/schema-entities/entity-names.md)을 따릅니다.

## <a name="rename-columns"></a>열 이름 바꾸기

동일한 테이블에 있는 여러 기존 열의 이름을 변경 합니다.

**구문**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *Col2*]] `,` ...

명령을 사용 하 여 두 열의 이름을 바꿀 수 있습니다. 각 열의 이름은 다른 이름으로 바뀝니다.