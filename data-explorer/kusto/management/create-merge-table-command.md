---
title: . create-merge 테이블-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 create-merge 테이블에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 554f6ed623b5a3be12a360fab0b1d5aa6eb4c084
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320964"
---
# <a name="create-merge-table"></a>.create-merge table

새 테이블을 만들거나 기존 테이블을 확장 합니다. 

명령은 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다. 

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

## <a name="syntax"></a>Syntax

`.create-merge``table` *TableName* ([columnName: columnType], ...)  [ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*] `)` ]

테이블이 없는 경우는 명령으로 정확 하 게 작동 `.create table` 합니다.

테이블 T가 있는 경우 `.create-merge table T (<columns specification>)` 명령을 보내려면 다음을 수행 합니다.

* <columns specification>이전에 t에 존재 하지 않은의 모든 열이 t의 스키마 끝에 추가 됩니다.
* 에 없는 T의 모든 열은 <columns specification> t에서 제거 되지 않습니다.
* T에는 <columns specification> 있지만 데이터 형식이 다른의 모든 열은 명령이 실패 합니다.

## <a name="see-also"></a>참고 항목

* [`.create-merge tables`](create-merge-tables-command.md)
* [`.create table`](create-table-command.md)
