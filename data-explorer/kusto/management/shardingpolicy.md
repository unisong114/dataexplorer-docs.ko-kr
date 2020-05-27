---
title: 데이터 분할 정책-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 분할 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 26ccf94f8d88c6c86a6c11c20ec9cc04f8af1a42
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011502"
---
# <a name="data-sharding-policy"></a>데이터 분할 정책

분할 정책은 Azure 데이터 탐색기 클러스터의 [익스텐트 (데이터 분할)](../management/extents-overview.md) 가 봉인 되어야 하는 경우를 정의 합니다.

> [!NOTE]
> 이 정책은 [데이터](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)수집 명령과 같이 새 익스텐트를 만드는 모든 작업에 적용 되며 [merge 및 rebuild 명령을](../management/extents-commands.md#merge-extents) 수행 합니다.

데이터 분할 정책에는 다음 속성이 포함 되어 있습니다.

- **Maxrowcount**:
    - 수집 또는 다시 작성 작업으로 만들어진 익스텐트의 최대 행 수입니다.
    - 기본값은 75만입니다.
    - [병합 작업](mergepolicy.md)에는 **적용 되지 않습니다** .
        - Merge 작업으로 만들어진 익스텐트의 행 수를 제한 해야 하는 경우 `RowCountUpperBoundForMerge` 엔터티의 [익스텐트 병합 정책](mergepolicy.md)에서 속성을 조정 합니다.
- **MaxExtentSizeInMb**:
    - 병합 작업으로 만들어진 익스텐트의 최대 허용 압축 데이터 크기 (mb)입니다.
    - ** [병합](mergepolicy.md) 작업에만**적용 됩니다.
    - 기본값은 1024 (1GB)입니다.

- **Maxoriginalsizeinmb**:
    - 다시 작성 작업으로 만들어진 익스텐트의 최대 허용 원래 데이터 크기 (mb)입니다.
    - ** [다시 빌드](mergepolicy.md) 작업에만**적용 됩니다.
    - 기본값은 2048 (2GB)입니다.

> [!WARNING]
> 데이터 분할 정책을 변경 하기 전에 Azure 데이터 탐색기 팀에 문의 하세요.

데이터베이스를 만들 때 기본 데이터 분할 정책이 포함 됩니다. 이 정책은 해당 정책이 테이블 수준에서 명시적으로 재정의 되지 않는 한 데이터베이스에 생성 된 모든 테이블에 상속 됩니다.

[분할 정책 제어 명령](../management/sharding-policy.md))을 사용 하 여 데이터베이스 및 테이블에 대 한 데이터 분할 정책을 관리 합니다.
 