---
title: 데이터 샤딩 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 샤딩 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6ed9cf3a1667fb57271a44dcfe7c6dd5318c4010
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520029"
---
# <a name="data-sharding-policy"></a>데이터 샤딩 정책

샤딩 정책은 Azure 데이터 탐색기 클러스터의 [익스텐트(데이터 샤드)를](../management/extents-overview.md) 봉인할 지 및 방법을 정의합니다.

> [!NOTE]
> 이 정책은 [데이터 수집](../management/data-ingestion/index.md)명령 및 [.merge 및 .rebuild 명령과](../management/extents-commands.md#merge-extents) 같은 새 익스텐션을 만드는 모든 작업에 적용됩니다.

데이터 샤딩 정책에는 다음 속성이 포함됩니다.

- **맥스로우카운트**:
    - 생성 또는 재생성 작업에 의해 생성된 익스텐트에 대한 최대 행 수입니다.
    - 기본값은 750,000입니다.
    - [병합 작업에는](mergepolicy.md) **적용되지 않습니다.**
        - 병합 작업에서 만든 익스텐트의 행 수를 제한해야 `RowCountUpperBoundForMerge` 하는 경우 엔터티의 [익스텐트 병합 정책에서 속성을 조정합니다.](mergepolicy.md)
- **맥스익스익사이즈인Mb**:
    - 병합 작업에서 생성된 범위에 대해 허용되는 최대 압축 데이터 크기(메가바이트)입니다.
    - 병합 **작업에 [merge](mergepolicy.md) 대해서만**적용됩니다.
    - 기본값은 1,024(1GB)입니다.

- **맥스 오리지널사이즈인Mb**:
    - 재생성 작업에 의해 생성된 익스텐트에 대해 허용되는 원본 데이터 크기(메가바이트)를 최대로 허용합니다.
    - ** [재생성](mergepolicy.md) 작업에 대해서만**적용됩니다.
    - 기본값은 2,048(2GB)입니다.

> [!WARNING]
> 데이터 샤딩 정책을 변경하기 전에 Azure 데이터 탐색기 팀과 상의하십시오.

데이터베이스를 만들 때 기본 데이터 샤딩 정책이 포함됩니다. 이 정책은 데이터베이스에서 만든 모든 테이블에 의해 상속됩니다(테이블 수준에서 정책이 명시적으로 재정의되지 않는 한).

[샤딩 정책 제어 명령)을](../management/sharding-policy.md)사용하여 데이터베이스 및 테이블에 대한 데이터 샤딩 정책을 관리합니다.
 