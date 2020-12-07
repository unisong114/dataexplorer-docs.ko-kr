---
title: 데이터베이스 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9dc2e1bf489123ddd66c9a203d74284e36eda53e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320947"
---
# <a name="databases-management"></a>데이터베이스 관리

이 항목에서는 다음과 같은 데이터베이스 제어 명령을 설명 합니다.

|명령 |설명 |
|--------|------------|
|[`.show databases`](show-databases.md) |모든 레코드가 사용자가 액세스할 수 있는 클러스터의 데이터베이스에 해당 하는 테이블을 반환 합니다.|
|[`.show database`](show-database.md) |컨텍스트 데이터베이스의 속성을 보여 주는 테이블을 반환 합니다. |
|[`.show cluster databases`](show-cluster-database.md) |클러스터에 연결 되어 있고 명령을 호출 하는 사용자가 액세스할 수 있는 모든 데이터베이스를 표시 하는 테이블을 반환 합니다. |
|[`.alter database`](alter-database.md) |데이터베이스의 친숙 한 이름 변경 |
|[`.show database schema`](show-schema-database.md) |단일 테이블 또는 JSON 개체의 모든 테이블 및 열을 사용 하 여 선택한 데이터베이스 구조에 대 한 단순 목록을 반환 합니다. |