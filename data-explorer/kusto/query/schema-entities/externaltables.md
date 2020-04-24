---
title: 외부 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f4a059ba4533ed91353e75b499e564e6dd06d591
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509387"
---
# <a name="external-tables"></a>외부 테이블

**외부 테이블은** Kusto 데이터베이스 외부에 저장된 데이터를 참조하는 Kusto 스키마 엔터티입니다.

[테이블과](tables.md)마찬가지로 외부 테이블에는 잘 정의된 스키마(열 이름 및 데이터 형식 쌍의 정렬된 목록)가 있습니다. 테이블과 달리 데이터는 Kusto 클러스터 외부에 저장되고 관리됩니다. 가장 일반적으로 데이터는 CSV, 마루, 아브로와 같은 일부 표준 형식으로 저장되며 Kusto에서 섭취하지 않습니다.

**외부 테이블은** 한 번 만들어지며(외부 [테이블 컨트롤 명령](../../management/externaltables.md)참조) [external_table()](../../query/externaltablefunction.md) 함수를 사용하여 이름으로 참조할 수 있습니다. 

**참고 사항**

* 외부 테이블 이름은 대/소문자를 구분합니다.
* 외부 테이블 이름은 Kusto 테이블 이름과 겹칠 수 없습니다.
* 외부 테이블 이름은 [엔터티 이름에](./entity-names.md)대한 규칙을 따릅니다.
* 데이터베이스당 외부 테이블의 최대 제한은 1,000입니다.
* Kusto는 [외부 테이블로 데이터를 내보내고](../../management/data-export/export-data-to-an-external-table.md) [외부 테이블을 쿼리할 수 있도록](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)지원합니다.
