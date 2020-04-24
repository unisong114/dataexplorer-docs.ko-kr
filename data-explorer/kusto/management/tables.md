---
title: 테이블 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 테이블 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 7b7e4c5c7111354864aa939ece76be2ab0a8ac15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519570"
---
# <a name="tables-management"></a>테이블 관리

이 항목에서는 테이블 및 관련 제어 명령의 수명 주기에 대해 설명합니다.

자세한 내용은 아래 표의 링크를 선택하십시오.

| 명령                                                                                                                 | 작업(Operation)                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | 테이블 표시 속성 관리 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | 섭취 매핑 관리        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | 테이블 만들기/수정/놓기 테이블       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | 데이터베이스의 테이블 내문 지정  |
| `.ingest`, `.set`, `.append`, `.set-or-append`(자세한 내용은 [데이터 수집](./data-ingestion/index.md) 참조))                                                                                                                                                                                      | 테이블로 데이터 수집     |

## <a name="crud-naming-conventions-for-tables"></a>테이블에 대한 CRUD 명명 규칙 
(위의 표에 링크된 섹션의 전체 세부 정보를 참조하십시오.)
 
| 명령 구문                             | 의미 체계                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | 해당 형식 및 이름의 엔터티가 있는 경우 엔터티를 반환합니다. 그렇지 않으면 엔터티를 만듭니다.                          |
| `.create-merge entityType entityName...`   | 해당 형식 및 이름의 엔터티가 있는 경우 기존 엔터티를 지정된 엔터티와 병합합니다. 그렇지 않으면 엔터티를 만듭니다. |
| `.alter entityType entityName ...`         | 해당 형식 및 이름의 엔터티가 없으면 오류가 발생합니다. 그렇지 않으면 지정된 엔터티로 바꿉습니다.            |
| `.alter-merge entityType entityName ...`   | 해당 형식 및 이름의 엔터티가 없으면 오류가 발생합니다. 그렇지 않으면 지정된 엔터티와 병합합니다.              |
| `.drop entityType entityName ...`          | 해당 형식 및 이름의 엔터티가 없으면 오류가 발생합니다. 그렇지 않으면 놓습니다.                                         |
| `.drop entityType entityName ifexists ...` | 해당 형식 및 이름의 엔터티가 없으면 반환합니다. 그렇지 않으면 놓습니다.                                        |
 
> [!NOTE]
> "병합"은 두 엔터티의 논리적 병합입니다.
>
> * 속성이 한 엔터티에 대해 정의되지만 다른 엔터티에 대해 정의되지 않은 경우 병합된 엔터티의 원래 값으로 표시됩니다.
> * 속성이 두 엔터티모두에 대해 정의되고 둘 다에 동일한 값을 가지는 경우 병합된 엔터티에 해당 값이 한 번 나타납니다.
> * 두 엔터티에 대해 속성이 정의되었지만 값이 다른 경우 오류가 발생합니다.