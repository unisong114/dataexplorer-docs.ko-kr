---
title: .show 데이터베이스 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .show 데이터베이스에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1827c3ea20d984c6846ef9feb603398020efe275
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610192"
---
# <a name="show-databases"></a>데이터베이스 표시

모든 레코드가 사용자가 액세스할 수 있는 클러스터의 데이터베이스에 해당하는 테이블을 반환합니다.

컨텍스트 데이터베이스의 속성을 보여 줄 테이블을 반환하려면 [.show 데이터베이스 를 참조하십시오.](show-database.md)

**구문**

`.show` `databases`

**출력 스키마입니다.**

|열 이름       |열 유형|Description                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |클러스터에 연결된 데이터베이스의 이름입니다.                         |
|영구 스토리지 |`string`   |데이터베이스의 영구 저장소 "루트"입니다. 내부 전용입니다.      |
|버전           |`string`   |데이터베이스의 버전입니다. 내부 전용입니다.                        |
|IsCurrent         |`bool`     |이 데이터베이스가 요청의 데이터베이스 컨텍스트인지 여부입니다.                |
|데이터베이스액세스모드|`string`   |`ReadWrite`의 하나 `ReadOnly` `ReadOnlyFollowing`, `ReadWriteEphemeral`에 이 .|
|예쁜 이름        |`string`   |데이터베이스의 예쁜 이름(있는 경우)입니다.                                    |
|예약슬롯1     |`bool`     |예약되어 있습니다. 내부 전용입니다.                                           |
|DatabaseId        |`guid`     |데이터베이스에 대한 전역고유 식별자입니다. 내부 전용입니다.      |
