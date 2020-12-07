---
title: . 클러스터 데이터베이스 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 클러스터 데이터베이스 표시를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c6d25380a44a2195f407c52a2224ee28fad9f8bb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320505"
---
# <a name="show-cluster-databases"></a>.show cluster databases

클러스터에 연결 되어 있으며 명령을 호출 하는 사용자가 액세스할 수 있는 모든 데이터베이스를 표시 하는 테이블을 반환 합니다. 특정 데이터베이스 이름을 사용 하는 경우 해당 데이터베이스만 포함 됩니다.

**구문**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `databases` `(` database1 `,` database2 `,` ... databaseN`)`

**출력**
 
|출력 매개 변수 |형식 |설명 
|---|---|---
|DatabaseName  |String |데이터베이스 이름 데이터베이스 이름은 대/소문자를 구분 합니다. 
|PersistentStorage  |String |데이터베이스가 저장 되는 영구 저장소 URI입니다. 이 필드는 임시 데이터베이스에 대해 비어 있습니다. 
|버전  |String |데이터베이스 버전 번호입니다. 이 번호는 데이터베이스의 각 변경 작업 (예: 데이터 추가 및 스키마 변경)에 대해 업데이트 됩니다. 
|IsCurrent  |부울 |현재 연결이 가리키는 데이터베이스 이면 True입니다. 
|DatabaseAccessMode  |String |클러스터가 데이터베이스에 연결 되는 방법입니다. 예를 들어 데이터베이스가 ReadOnly 모드로 연결 되어 있는 경우 클러스터는 모든 요청에서 데이터베이스를 수정 하는 것을 실패 합니다. 
|PrettyName |String |데이터베이스 이름입니다.
|CurrentUserIsUnrestrictedViewer |부울 | 현재 사용자가 데이터베이스에서 제한 없는 뷰어 인지 여부를 지정 합니다.
|DatabaseId |Guid |데이터베이스의 고유 ID입니다.