---
title: 쿼리 문-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 문을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 778b8c276d6264b890c0b0be7b127f578663a64d
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129059"
---
# <a name="query-statement-types"></a>쿼리 문 형식

::: zone pivot="azuredataexplorer"

쿼리는 세미콜론 ()으로 구분 된 하나 이상의 **쿼리 문으로**구성 됩니다 `;` .
이러한 쿼리 문 중 하나 이상이 [테이블 형식 식 문](./tabularexpressionstatements.md)이어야 합니다.
테이블 형식 식 문은 하나 이상의 테이블 형식 결과를 생성 합니다.
쿼리에 테이블 형식 식 문이 두 개 이상 있으면 쿼리에 테이블 형식 식 문이 [일괄 처리](./batches.md) 되 고 이러한 문에 의해 생성 된 표 형식 결과가 모두 쿼리에서 반환 됩니다.

다음과 같은 두 가지 유형의 쿼리 문이 있습니다.

* 사용자가 주로 사용 하는 문 ([사용자 쿼리 문](#user-query-statements))
* 중간 계층 응용 프로그램이 사용자 쿼리를 수행 하 고 수정 된 버전을 Kusto ([application query 문](#application-query-statements))로 보내는 시나리오를 지원 하도록 디자인 된 문입니다.

일부 쿼리 문은 두 시나리오에서 모두 유용 합니다.

> [!NOTE]
> 쿼리 문의 "효과"는 문이 쿼리에 표시 되 고 쿼리 끝에서 종료 되는 지점에서 시작 됩니다. 쿼리가 완료 되 면 모든 리소스가 해제 되 고 이후의 쿼리에는 영향을 주지 않습니다. 예를 들어 쿼리를 모든 쿼리의 로그에 기록 하거나 결과를 캐시 하 여 쿼리를 실행 하는 등의 부작용이 발생 하지 않습니다.

## <a name="user-query-statements"></a>사용자 쿼리 문

다음은 사용자 쿼리 문 목록입니다.

* [Let 문은](./letstatement.md) 이름과 식 간의 바인딩을 정의 합니다.
  Let 문을 사용 하면 긴 쿼리를 더 쉽게 이해할 수 있는 작은 이름의 작은 부분으로 나눌 수 있습니다.

* [Set 문은](./setstatement.md) 쿼리가 처리 되는 방식과 결과가 반환 되는 방법에 영향을 주는 쿼리 옵션을 설정 합니다.

* 가장 중요 한 쿼리 문인 [테이블 형식 식 문은](./tabularexpressionstatements.md)"흥미로운" 데이터를 결과로 다시 반환 합니다.

## <a name="application-query-statements"></a>응용 프로그램 쿼리 문

다음은 응용 프로그램 쿼리 문 목록입니다.

* [별칭 문이](./aliasstatement.md) 동일한 클러스터 또는 원격 클러스터에 있는 다른 데이터베이스에 대 한 별칭을 정의 합니다.

* Kusto를 기반으로 하는 응용 프로그램에서 사용할 수 있는 [패턴 문으로](./patternstatement.md), 사용자에 게 쿼리 언어를 노출 하 여 쿼리 이름 확인 프로세스에 자신을 삽입할 수 있습니다.

* [쿼리 매개 변수 문은](./queryparametersstatement.md)삽입 공격 으로부터 자신을 보호 하기 위해 Kusto를 기반으로 하는 응용 프로그램에서 사용 됩니다. 명령 매개 변수가 sql 삽입 공격 으로부터 sql을 보호 하는 방법과 유사 합니다.

* Kusto를 기반으로 하는 응용 프로그램에서 특정 열과 레코드에 대 한 액세스를 제한 하는 것을 포함 하 여 Kusto의 특정 데이터 하위 집합에 대 한 쿼리를 제한 하는 [restrict 문](./restrictstatement.md)입니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
