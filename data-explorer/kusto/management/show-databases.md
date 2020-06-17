---
title: . 데이터베이스 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에 데이터베이스를 표시 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5ff64db05bb85d88ed3da3347ea95cf02b0234b
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818539"
---
# <a name="show-databases"></a>.show databases

모든 레코드가 사용자가 액세스할 수 있는 클러스터의 데이터베이스에 해당 하는 테이블을 반환 합니다.

컨텍스트 데이터베이스의 속성을 보여 주는 테이블은를 참조 하십시오 [`.show database`](show-database.md) .

**구문**

`.show` `databases`

**출력 스키마입니다.**

|열 이름       |열 유형|Description                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |클러스터에 연결 된 데이터베이스의 이름입니다.                    |
|PersistentStorage |`string`   |데이터베이스의 영구 저장소 "root"입니다. 내부용으로만 사용할 수 있습니다.          |
|버전           |`string`   |데이터베이스의 버전입니다. 내부용으로만 사용할 수 있습니다.                       |
|IsCurrent         |`bool`     |이 데이터베이스가 요청의 데이터베이스 컨텍스트 인지 여부                    |
|DatabaseAccessMode|`string`   |,, `ReadWrite` `ReadOnly` `ReadOnlyFollowing` 또는 중 하나입니다.`ReadWriteEphemeral`    |
|PrettyName        |`string`   |데이터베이스의 이름입니다 (있는 경우).                        |
|ReservedSlot1     |`bool`     |예약되어 있습니다. 내부용으로만 사용할 수 있습니다.              |
|DatabaseId        |`guid`     |데이터베이스의 guid (globally unique identifier)입니다. 내부용으로만 사용할 수 있습니다.          |
