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
ms.openlocfilehash: 19dc7db9e344a516b5c92917dccbf8362b1ca858
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102873"
---
# <a name="create-merge-table"></a>.create-merge table

새 테이블을 만들거나 기존 테이블을 확장 합니다. 

명령은 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다. 

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

## <a name="syntax"></a>구문

`.create-merge``table` *TableName* ([columnName: columnType], ...)  [ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*] `)` ]

테이블이 없는 경우는 "create table" 명령으로 정확 하 게 작동 합니다.

테이블이 T가 있는 경우 ". create-merge table T ( <columns specification> )" 명령을 보내려면 다음을 수행 합니다.

* <columns specification>이전에 t에 존재 하지 않은의 모든 열이 t의 스키마 끝에 추가 됩니다.
* 에 없는 T의 모든 열은 <columns specification> t에서 제거 되지 않습니다.
* T에는 <columns specification> 있지만 데이터 형식이 다른의 모든 열은 명령이 실패 합니다.

## <a name="see-also"></a>참고 항목

* [.create-merge tables](create-merge-tables-command.md)
* [. create table](create-table-command.md)
