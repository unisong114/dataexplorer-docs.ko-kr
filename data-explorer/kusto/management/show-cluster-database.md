---
title: .show 클러스터 데이터베이스 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .show 클러스터 데이터베이스에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f354862df1bc9bef352819832125cf6f82ba0ae4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519995"
---
# <a name="show-cluster-databases"></a>클러스터 데이터베이스 .show

클러스터에 연결된 모든 데이터베이스와 명령을 호출하는 사용자가 액세스 권한이 있는 데이터베이스를 보여 줄 테이블을 반환합니다. 특정 데이터베이스 이름을 사용하는 경우 해당 데이터베이스만 포함됩니다.

**구문**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `,` 데이터베이스1`,` 데이터베이스2 ... `databases` `(` 데이터베이스N`)`

**출력**
 
|출력 매개 변수 |Type |Description 
|---|---|---
|DatabaseName  |String |데이터베이스 이름 데이터베이스 이름은 대/소문자를 구분합니다. 
|영구 스토리지  |String |데이터베이스가 저장되는 영구 저장소 URI입니다. 이 필드는 임시 데이터베이스에 대해 비어 있습니다. 
|버전  |String |데이터베이스 버전 번호입니다. 이 번호는 데이터베이스의 각 변경 작업(예: 데이터 추가 및 스키마 변경)에 대해 업데이트됩니다. 
|IsCurrent  |부울 |데이터베이스가 현재 연결이 가리키는 데이터베이스인 경우 true입니다. 
|데이터베이스액세스모드  |String |클러스터가 데이터베이스에 연결되는 방식입니다. 예를 들어 데이터베이스가 ReadOnly 모드에 연결되어 있는 경우 클러스터는 어떤 방식으로든 데이터베이스를 수정하는 모든 요청에 실패합니다. 
|예쁜 이름 |String |데이터베이스 꽤 이름입니다.
|현재사용자IsUn제한뷰어 |부울 | 현재 사용자가 데이터베이스의 무제한 뷰어인지 지정합니다.
|DatabaseId |Guid |데이터베이스의 고유 ID입니다.