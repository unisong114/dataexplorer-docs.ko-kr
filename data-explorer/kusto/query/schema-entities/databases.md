---
title: 데이터베이스-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 3d981a4b56eef689081a4bc6a622221c126efa2e
ms.sourcegitcommit: 8b8228fe18e6408673891374a8048a7a3723921c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96516067"
---
# <a name="databases"></a>데이터베이스

Kusto는 상위 수준 엔터티가 인 데이터를 저장 하는 관계 모델을 따릅니다 `database` . 

Kusto 클러스터는 여러 데이터베이스를 호스팅할 수 있습니다. 여기서 각 데이터베이스는 고유한 [테이블](tables.md)컬렉션, [저장 된 함수](stored-functions.md)및 [외부 테이블](externaltables.md)을 호스팅합니다.
각 데이터베이스에는 [RBAC (역할 기반 Access Control) 모델](../../management/access-control/index.md) 을 기반으로 하는 고유한 사용 권한이 설정 되어 있습니다.

**참고**  

* 데이터베이스 이름은 [엔터티 이름](./entity-names.md)규칙을 따라야 합니다.
* 클러스터당 최대 데이터베이스 제한은 1만입니다.
