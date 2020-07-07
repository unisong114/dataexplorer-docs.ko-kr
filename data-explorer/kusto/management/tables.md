---
title: 테이블 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 테이블 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 4de0e749ad47b8f2e3f2c0f26d5d18466efaff97
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967301"
---
# <a name="tables-management"></a>테이블 관리

이 항목에서는 테이블을 탐색, 생성 및 변경 하는 데 도움이 되는 테이블 및 연결 된 제어 명령의 수명 주기에 대해 설명 합니다.

아래 표에서 링크를 선택 하 여 자세한 내용을 참조 하십시오.

| 명령                                                                                                                 | 작업                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | 테이블 표시 속성 관리 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | 수집 매핑 관리        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | 테이블 만들기/수정/삭제       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | 데이터베이스의 테이블 열거  |
| `.ingest`, `.set`, `.append`, `.set-or-append`(자세한 내용은 [데이터 수집](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) 참조))                                                                                                                                                                                      | 테이블에 데이터 수집     |

## <a name="crud-naming-conventions-for-tables"></a>테이블에 대 한 CRUD 명명 규칙 
위의 표에 나와 있는에 연결 된 섹션에서 전체 세부 정보를 참조 하세요.
 
| 명령 구문                             | 의미 체계                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | 해당 형식 및 이름의 엔터티가 있는 경우 엔터티를 반환 합니다. 그렇지 않으면 엔터티를 만듭니다.                          |
| `.create-merge entityType entityName...`   | 해당 형식 및 이름의 엔터티가 있는 경우 기존 엔터티를 지정 된 엔터티와 병합 합니다. 그렇지 않으면 엔터티를 만듭니다. |
| `.alter entityType entityName ...`         | 해당 형식 및 이름의 엔터티가 없는 경우 오류가 발생 합니다. 그렇지 않으면 지정 된 엔터티로 바꿉니다.            |
| `.alter-merge entityType entityName ...`   | 해당 형식 및 이름의 엔터티가 없는 경우 오류가 발생 합니다. 그렇지 않으면 지정 된 엔터티와 병합 합니다.              |
| `.drop entityType entityName ...`          | 해당 형식 및 이름의 엔터티가 없는 경우 오류가 발생 합니다. 그렇지 않으면 삭제 합니다.                                         |
| `.drop entityType entityName ifexists ...` | 해당 형식 및 이름의 엔터티가 없으면를 반환 합니다. 그렇지 않으면 삭제 합니다.                                        |
 
> [!NOTE]
> "Merge"는 두 엔터티의 논리적 병합입니다.
>
> * 속성이 하나의 엔터티에 대해서만 정의 되 고 다른 엔터티에 대해서는 정의 되지 않은 경우에는 병합 된 엔터티에서 원래 값과 함께 표시 됩니다.
> * 두 엔터티에 대해 속성이 모두 정의 되 고 두 엔터티에 동일한 값이 있는 경우 병합 된 엔터티에 해당 값을 사용 하 여 한 번 표시 됩니다.
> * 속성이 두 엔터티 모두에 대해 정의 되었지만 값이 다른 경우 오류가 발생 합니다.