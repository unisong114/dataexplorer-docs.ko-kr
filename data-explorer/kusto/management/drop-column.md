---
title: 드롭 열 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 드롭 열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 56ba5499b27b517b3080ee27ac317aa1e0917edd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521219"
---
# <a name="drop-column"></a>드롭 열

테이블에서 열을 제거합니다.
테이블에서 여러 열을 삭제하려면 [아래를](#drop-table-columns)참조하십시오.

> [!WARNING]
> 이 명령은 되돌릴 수 없습니다. 제거된 열의 모든 데이터가 삭제됩니다.
> 해당 열을 다시 추가하는 이후 명령은 데이터를 복원할 수 없습니다.

**구문**

`.drop``column` *테이블 이름* `.` *열 이름*

## <a name="drop-table-columns"></a>테이블 열 삭제

테이블에서 여러 열을 제거합니다.

> [!WARNING]
> 이 명령은 되돌릴 수 없습니다. 제거된 열의 모든 데이터가 삭제됩니다.
> 이러한 열을 다시 추가하는 이후 명령은 데이터를 복원할 수 없습니다.

**구문**

`.drop``table` *테이블 네임* `columns` `(` `,` *콜1* [ *콜2*]...`)`