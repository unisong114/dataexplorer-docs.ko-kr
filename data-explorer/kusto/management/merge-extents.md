---
title: . 병합 익스텐트-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 병합 익스텐트 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 6b633358713b0ff48d14fc9c5ca2a907bad0afab
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060760"
---
# <a name="merge-extents"></a>. 범위 병합

이 명령은 지정 된 테이블에서 해당 Id가 나타내는 범위를 병합 합니다. 

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

## <a name="syntax"></a>Syntax

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

다음과 같은 두 가지 유형의 병합 작업이 있습니다.
* 인덱스를 다시 작성 하는 *Merge*
* 데이터를 완전히 reingests 하는 *다시 빌드*

다음 세 가지 옵션을 사용할 수 있습니다.
* `async`: 비동기적으로 명령을 실행 합니다. 
    * 작업 ID (Guid)가 반환 됩니다.
    * 작업 상태를 모니터링할 수 있습니다. 명령을 사용 하 여 작업 ID를 사용 `.show operations <operation ID>` 합니다.
* `dryrun`: 작업은 병합 되어야 하는 익스텐트만 나열 하지만 실제로 병합 하지는 않습니다.
* `with(rebuild=true)`: 익스텐트가 다시 작성 되 고 데이터가 병합 되지 않고 reingested 됩니다. 인덱스가 다시 작성 됩니다.

## <a name="return-output"></a>반환 출력

출력 매개 변수 |형식 |설명
---|---|---
OriginalExtentId |string |원본 테이블에서 원본 익스텐트의 원래 범위에 대 한 고유 식별자 (GUID)로, 대상 범위에 병합 되었습니다.
ResultExtentId |string |원본 범위에서 만들어진 익스텐트의 고유 식별자 (GUID)입니다. 실패 시: "실패" 또는 "중단 됨"입니다.
기간 |timespan |병합 작업을 완료 하는 데 걸린 시간입니다.

## <a name="examples"></a>예

### <a name="rebuild-two-specific-extents-in-mytable-asynchronously"></a>에서 두 개의 특정 익스텐트 `MyTable` 를 비동기적으로 다시 빌드합니다.

```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

### <a name="merge-two-specific-extents-in-mytable-synchronously"></a>에서 두 개의 특정 익스텐트 `MyTable` 를 동기적으로 병합 합니다.

```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

## <a name="notes"></a>참고

* 일반적으로 `.merge` 명령을 수동으로 실행 해서는 안 됩니다. 테이블 및 데이터베이스에 대 한 [병합 정책](mergepolicy.md) 에 따라 명령은 지속적으로 클러스터의 백그라운드에서 자동으로 실행 됩니다.  
  * 여러 익스텐트를 단일 항목으로 병합 하는 기준에 대 한 자세한 내용은 [병합 정책](mergepolicy.md)을 참조 하세요.
* `.merge`작업은 `Abandoned` 작업 ID로 실행 될 때 볼 수 있는의 최종 상태를 가집니다 `.show operations` . 원본 익스텐트가 원본 테이블에 더 이상 존재 하지 않기 때문에이 상태에서는 원본 익스텐트가 병합 되지 않은 것으로 제안 합니다. `Abandoned`상태는 다음과 같은 경우에 발생 합니다.
   * 원본 익스텐트가 테이블 보존의 일부로 삭제 되었습니다.
   * 원본 익스텐트가 다른 테이블로 이동 되었습니다.
   * 원본 테이블이 완전히 삭제 되거나 이름이 바뀌었습니다.
