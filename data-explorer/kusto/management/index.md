---
title: 관리(제어 명령) 개요 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 관리(제어 명령) 개요에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7a0a2384409f4b1c7e55231d8bf7b68c44ab8a6d
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303273"
---
# <a name="management-control-commands-overview"></a>관리(제어 명령) 개요

이 섹션에서는 Kusto를 관리하는 데 사용되는 제어 명령에 대해 설명합니다.
제어 명령은 데이터베이스 테이블에서 반드시 데이터일 필요가 없는 정보를 검색하거나 서비스 상태 등을 수정할 수 있는 서비스에 대한 요청입니다.

## <a name="differentiating-control-commands-from-queries"></a>제어 명령과 쿼리의 구분

Kusto는 언어 수준, 프로토콜 수준 및 API 수준의 세 가지 메커니즘을 사용하여 쿼리와 제어 명령을 구분합니다. 이 작업은 보안상의 목적으로 수행됩니다.

언어 수준에서는 요청 텍스트의 첫 번째 문자가 요청이 제어 명령 또는 쿼리인지의 여부를 결정합니다. 제어 명령은 점(`.`) 문자로 시작해야 하며, 쿼리는 이 문자로 시작할 수 없습니다.

프로토콜 수준에서는 서로 다른 HTTP/HTTPS 엔드포인트가 쿼리가 아닌 제어 명령에 사용됩니다.

API 수준에서는 다양한 함수가 쿼리가 아닌 제어 명령을 보내는 데 사용됩니다.

## <a name="combining-queries-and-control-commands"></a>제어 명령과 쿼리의 결합

제어 명령은 쿼리를 참조하거나(그 반대로는 참조할 수 없음), 다른 제어 명령을 참조할 수 있습니다.
지원되는 몇 가지 시나리오는 다음과 같습니다.

1. **AdminThenQuery**: 제어 명령이 실행되고, 해당 결과(임시 데이터 테이블로 표시됨)가 쿼리의 입력으로 사용됩니다.
2. **AdminFromQuery**: 쿼리 또는 `.show` 관리 명령이 실행되고, 해당 결과(임시 데이터 테이블로 표시됨)가 제어 명령의 입력으로 사용됩니다.

모든 경우에서 전체 조합은 기술적으로 쿼리가 아니라 제어 명령이므로 요청 텍스트는 점(`.`) 문자로 시작해야 하며, 요청은 서비스의 관리 엔드포인트로 보내야 합니다.

또한 [쿼리 문](../query/statements.md)은 텍스트의 쿼리 부분 내에 표시됩니다(명령 자체 앞에 나올 수 없음).

>[!NOTE]
> [command-then-query](명령 후 쿼리) 작업을 너무 자주 실행하지 마세요.
> *command-then-query* 는 제어 명령의 결과 세트를 파이프하고, 이 세트에 대한 필터/집계를 적용합니다.
>  * 예: `.show ... | where ... | summarize ...`
>   * `.show cluster extents | count`(`| count`에 강조)와 같은 작업을 실행하는 경우 Kusto는 먼저 클러스터의 모든 익스텐트에 대한 모든 세부 정보가 포함된 데이터 테이블을 준비합니다. 그런 다음, 시스템에서 메모리 내 전용 테이블을 Kusto 엔진에 보내 계산을 수행합니다. 시스템은 실제로 최적화되지 않은 경로에서 활발하게 작동하여 이러한 간단한 대답을 제공합니다.


**AdminThenQuery** 는 다음 두 가지 방법 중 하나로 표시됩니다.

1. 파이프(`|`) 문자를 사용하는 경우 이로 인해 쿼리에서 제어 명령의 결과를 다른 데이터 생성 쿼리 연산자인 것처럼 처리합니다.
2. 세미콜론(`;`) 문자를 사용하는 경우 제어 명령의 결과를 `$command_results`라는 특수 기호에 도입합니다. 그러면 쿼리에서 이 기호를 여러 번 사용할 수 있습니다.

예를 들면 다음과 같습니다.

```kusto
// 1. Using pipe: Count how many tables are in the database-in-scope:
.show tables
| count

// 2. Using semicolon: Count how many tables are in the database-in-scope:
.show tables;
$command_results
| count

// 3. Using semicolon, and including a let statement:
.show tables;
let useless=(n:string){strcat(n,'-','useless')};
$command_results | extend LastColumn=useless(TableName)
```

**AdminFromQuery** 는 `<|` 문자 조합으로 표시됩니다. 예를 들어, 다음 예제에서는 먼저 단일 열(`string` 형식의 `str` 이름)과 단일 행이 있는 테이블을 생성하는 쿼리를 실행하고, 컨텍스트에서 이 테이블을 `MyTable` 이름으로 데이터베이스에 작성합니다.

```kusto
.set MyTable <|
let text="Hello, World!";
print str=text
```


