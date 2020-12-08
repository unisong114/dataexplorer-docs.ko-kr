---
title: 스키마 관리 모범 사례-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 스키마 관리 모범 사례를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: ba0d931fcca257cb3b5658354dfb8bf70dc6848f
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774625"
---
# <a name="best-practices-for-schema-management"></a>최선의 스키마 관리 방법

다음은 몇 가지 모범 사례입니다. 이를 통해 관리 명령이 더 잘 작동 하 고 서비스 리소스에 더 많은 영향을 줄 수 있습니다.

|작업  |기능  |사용하지 마세요. | 참고 |
|---------|---------|---------|----
| **여러 테이블 만들기**    |  단일 명령 사용 [`.create tables`](create-tables-command.md)       | 많은 명령 실행 안 함 `.create table`        | |
| **여러 테이블 이름 바꾸기**    | 단일 호출을 수행 합니다. [`.rename tables`](rename-table-command.md)        |  각 테이블 쌍에 대해 별도의 호출을 실행 하지 마십시오.   |    |
|**명령 표시**   |   가장 낮은 범위의 명령 사용 `.show` |   파이프 () 뒤에 필터를 적용 하지 않습니다. `|`   </ul></li>  | 가능한 한 많이 사용을 제한 합니다. 가능 하면 반환 하는 정보를 캐시 합니다. |
| 익스텐트 표시  | `.show table T extents` 사용   |`.show cluster extents | where TableName == 'T'`는 사용하지 마세요.  |
|  데이터베이스 스키마를 표시 합니다. |`.show database DB schema` 사용  |  `.show schema | where DatabaseName == 'DB'`는 사용하지 마세요. |
| **대량 스키마가 있는 클러스터에 스키마 표시** <br> |사용 [`.show databases schema`](../management/show-schema-database.md) |`.show schema`는 사용하지 마세요.| 예를 들어 100 개 이상의 데이터베이스를 포함 하는 클러스터에서를 사용 합니다.
| **테이블의 존재 여부를 확인 하거나 테이블의 스키마를 가져옵니다.**|`.show table T schema as json` 사용|`.show table T`은(는) 사용하지 마세요. |이 명령을 사용 하 여 단일 테이블에 대 한 실제 통계를 가져옵니다.|
| **값을 포함 하는 테이블의 스키마를 정의 합니다. `datetime`**  |관련 열을 형식으로 설정 합니다. `datetime` | 필터링을 `string` 위해 또는 숫자 열을 쿼리 시로 변환 하지 않음 `datetime` (수집 시간 전후에 수행할 수 있는 경우)|
| **메타 데이터에 범위 태그 추가** |자주 사용 |태그를 사용 하지 마십시오 `drop-by:` .이 경우 백그라운드에서 성능 중심의 정리 프로세스를 수행 하는 시스템 기능이 제한 됩니다.|  <br> [성능 정보](../management/extents-overview.md#extent-tagging)를 참조 하세요. |
