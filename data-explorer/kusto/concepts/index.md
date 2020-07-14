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
ms.openlocfilehash: a1382703c4c70263ff0d8a066860071fd07ef179
ms.sourcegitcommit: bf2c9da0c23ebcaec19b229d2079032d54a2cc82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86140496"
---
# <a name="getting-started-with-kusto"></a>Kusto 시작

Azure Data Explorer는 빅 데이터에 대한 대화형 분석을 저장하고 실행하는 서비스입니다.

이는 관계형 데이터베이스 관리 시스템을 기반으로 데이터베이스, 테이블 및 열과 같은 엔터티를 지원합니다. 복잡한 분석 쿼리는 Kusto 쿼리 언어를 사용하여 만들어집니다. 일부 쿼리 연산자는 다음을 포함합니다.
* 계산 열
* 행에서 검색 및 필터링
* 그룹별 집계
* 조인 함수

이 서비스는 뛰어난 데이터 수집 및 쿼리 성능을 제공합니다. 
* 또한 개별 행과 크로스 테이블 제약 조건 또는 트랜잭션의 원본 위치 업데이트를 수행할 수 있는 기능이 저하됩니다. 
* 이 서비스는 OLTP 및 데이터 웨어하우징과 같은 시나리오를 위해 기존 RDBMS 시스템을 바꾸는 것이 아니라 보완합니다.
* 구조적, 반구조적(예: JSON 같은 중첩 형식) 및 비구조적(자유 텍스트) 데이터를 동일하게 잘 처리합니다.

## <a name="interacting-with-azure-data-explorer"></a>Azure Data Explorer와 상호 작용

사용자가 Azure Data Explorer(Kusto)와 상호 작용하는 주요 방법:
* [쿼리 도구](../../tools-integrations-overview.md#azure-data-explorer-query-tools) 중 하나를 사용합니다. 
* [SQL 쿼리](../api/tds/t-sql.md).
*  [Kusto 쿼리 언어](../query/index.md)는 기본적인 상호 작용 수단입니다. KQL을 사용하면 데이터 쿼리를 보내고 [제어 명령](../management/index.md)을 사용하여 엔터티를 관리하고 메타데이터를 검색하는 등의 작업을 수행할 수 있습니다.
쿼리와 제어 명령은 둘 다 짧은 텍스트 "프로그램"입니다.

## <a name="kusto-queries"></a>Kusto 쿼리

쿼리는 데이터나 메타데이터를 수정하지 않고도 데이터를 처리하고 이 처리 결과를 반환하는 읽기 전용 요청입니다. Kusto 쿼리는 [SQL 언어](../api/tds/t-sql.md) 또는 [Kusto 쿼리 언어](../query/index.md)를 사용할 수 있습니다. 후자의 예로서, 다음 쿼리는 `Logs` 테이블의 `Level` 열에 문자열 `Critical`과 같은 값을 갖는 행 수를 계산합니다.

```kusto
Logs
| where Level == "Critical"
| count
```

> [!NOTE]
> 쿼리는 점(`.`) 문자 또는 해시(`#`) 문자로 시작할 수 없습니다.

## <a name="control-commands"></a>제어 명령

제어 명령은 데이터 또는 메타데이터를 처리하고 수정할 수도 있는 Kusto에 대한 요청입니다. 예를 들어 다음 제어 명령은 `Level` 및 `Text`라는 두 열이 있는 새 Kusto 테이블을 만듭니다.

```kusto
.create table Logs (Level:string, Text:string)
```

제어 명령에는 고유 구문이 있습니다. 이 둘에는 많은 개념을 공유하지만 Kusto 쿼리 언어 구문의 일부는 아닙니다. 특히 제어 명령은 명령 텍스트의 첫 번째 문자를 점(`.`) 문자(쿼리를 시작할 수 없음)로 사용하여 쿼리와 구별됩니다.
이러한 구별을 통해 여러 종류의 보안 공격이 방지됩니다. 단순히 쿼리 내에 제어 명령을 포함할 수 없도록 하기 때문입니다.

일부 제어 명령은 데이터 또는 메타데이터를 수정할 수 없습니다. `.show`로 시작하는 대규모 명령 클래스는 메타데이터 또는 데이터를 표시하는 데 사용됩니다. 예를 들어 `.show tables` 명령은 현재 데이터베이스의 모든 테이블 목록을 반환합니다.
