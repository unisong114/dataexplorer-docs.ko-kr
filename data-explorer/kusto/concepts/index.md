---
title: Kusto 시작
description: 이 문서는 Kusto를 시작하는 방법을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a56878c8b79e651afcd1b8ce18a3220dc304211
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863390"
---
# <a name="getting-started-with-kusto"></a>Kusto 시작

Azure Data Explorer는 빅 데이터에 대한 대화형 분석을 저장하고 실행하는 서비스입니다.

이는 관계형 데이터베이스 관리 시스템을 기반으로 데이터베이스, 테이블 및 열과 같은 엔터티를 지원합니다. 복잡한 분석 쿼리는 Kusto 쿼리 언어를 사용하여 수행됩니다. 일부 쿼리 연산자에는 계산 열, 검색 및 필터링 또는 행, 그룹별 집계 및 조인이 포함됩니다.

서비스는 개별 행 및 테이블 간 제약 조건/트랜잭션의 전체 업데이트를 수행할 수 있는 기능을 "포기"하여 탁월한 데이터 수집 및 쿼리 성능을 제공합니다. 따라서 OLTP 및 데이터 웨어하우징과 같은 시나리오를 위해 기존 RDBMS 시스템을 바꾸는 것이 아니라 보완합니다.

구조적, 반구조적(JSON 같은 중첩 형식) 및 비구조적(자유 텍스트) 데이터를 동일하게 잘 처리합니다.

## <a name="interacting-with-azure-data-explorer"></a>Azure Data Explorer와 상호 작용

사용자가 Kusto를 조작하는 주요 방법은 여러 [클라이언트 도구](../tools/index.md) 중 하나를 사용하는 것입니다. [SQL 쿼리](../api/tds/t-sql.md)가 지원되지만, 상호 작용의 주요 수단은 [Kusto 쿼리 언어](../query/index.md)를 사용하여 데이터 쿼리를 전송하고 [제어 명령](../management/index.md)을 사용하여 엔터티를 관리하고 메타데이터를 검색하는 것입니다. 쿼리와 제어 명령은 둘 다 기본적으로 짧은 텍스트 "프로그램"입니다.

## <a name="kusto-queries"></a>Kusto 쿼리

쿼리는 데이터나 메타데이터를 수정하지 않고도 데이터를 처리하고 이 처리 결과를 반환하는 읽기 전용 요청입니다. Kusto 쿼리는 [SQL 언어](../api/tds/t-sql.md) 또는 [Kusto 쿼리 언어](../query/index.md)를 사용할 수 있습니다. 후자의 예로 다음 쿼리는 `Logs` 테이블의 `Level` 열 값이 `Critical` 문자열과 같은 행의 수를 계산합니다.

```kusto
Logs
| where Level == "Critical"
| count
```

쿼리는 점 (`.`) 문자 또는 해시(`#`) 문자로 시작할 수 없습니다.

## <a name="control-commands"></a>제어 명령

제어 명령은 데이터 또는 메타데이터를 처리하고 수정할 수도 있는 Kusto에 대한 요청입니다. 예를 들어 다음 제어 명령은 `Level` 및 `Text`라는 두 열이 있는 새 Kusto 테이블을 만듭니다.

```kusto
.create table Logs (Level:string, Text:string)
```

제어 명령에는 고유 구문이 있습니다(이 둘은 많은 개념을 공유하지만 Kusto 쿼리 언어 구문의 일부는 아님). 특히 제어 명령은 명령 텍스트의 첫 번째 문자를 점(`.`) 문자(쿼리를 시작할 수 없음)로 사용하여 쿼리와 구별됩니다.
이러한 구별을 통해 여러 종류의 보안 공격이 방지됩니다. 단순히 쿼리 내에 제어 명령을 포함할 수 없도록 하기 때문입니다.

일부 제어 명령은 데이터 또는 메타데이터를 수정할 수 없습니다. `.show`로 시작하는 많은 종류의 명령은 메타데이터 또는 데이터를 표시하는 데 사용됩니다. 예를 들어 `.show tables` 명령은 현재 데이터베이스의 모든 테이블 목록을 반환합니다.
