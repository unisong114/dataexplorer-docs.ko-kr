---
title: 쿼리 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 문을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 20eeb1aa755fcf4e3068cba061a2738a375e1847
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765569"
---
# <a name="query-statements"></a>쿼리 문

::: zone pivot="azuredataexplorer"

쿼리는 세미콜론()으로`;`구분되는 하나 이상의 **쿼리 문으로**구성됩니다.
이러한 쿼리 문 중 하나 이상이 [테이블 형식 식 문이어야](./tabularexpressionstatements.md)합니다.
표식 문은 하나 이상의 테이블 형식 결과를 생성합니다.
쿼리에 둘 이상의 테이블 형식 식 문이 있는 경우 쿼리에는 테이블 형식 식 문 [일괄 처리가](./batches.md) 있고 이러한 문에서 생성된 테이블 형식 결과는 모두 쿼리에서 반환됩니다.

두 가지 유형의 쿼리 문:

* 사용자가 주로 사용하는 명령문(사용자[쿼리 문)](#user-query-statements)
* mid 계층 응용 프로그램이 사용자 쿼리를 받아 수정된 버전을 Kusto(응용[프로그램 쿼리 문)로](#application-query-statements)보내는 시나리오를 지원하도록 설계된 명령문입니다.

일부 쿼리 문은 두 시나리오모두에서 유용합니다.

> [!NOTE]
> 쿼리 문의 "효과"는 문이 쿼리에 나타나고 쿼리의 끝에서 끝나는 지점에서 시작됩니다. 쿼리가 완료되면 모든 리소스가 해제되고 향후 쿼리에 영향을 미치지 않습니다(예: 쿼리가 모든 쿼리의 로그에 기록되거나 결과를 캐시하는 등 부작용 제외).

## <a name="user-query-statements"></a>사용자 쿼리 문

다음은 사용자 쿼리 문의 목록입니다.

* [let 문은](./letstatement.md) 이름과 식 사이의 바인딩을 정의합니다.
  문을 사용하여 긴 쿼리를 이해하기 쉬운 작은 명명된 부분으로 나누도록 할 수 있습니다.

* [set 문은](./setstatement.md) 쿼리가 처리되고 결과가 반환되는 방식에 영향을 주는 쿼리 옵션을 설정합니다.

* 가장 중요한 쿼리 [문인 표식 식 문은](./tabularexpressionstatements.md)"흥미로운" 데이터를 결과로 반환합니다.

## <a name="application-query-statements"></a>응용 프로그램 쿼리 명령문

다음은 응용 프로그램 쿼리 문의 목록입니다.

* [별칭 문은](./aliasstatement.md) 동일한 클러스터 또는 원격 클러스터에서 다른 데이터베이스로 별칭을 정의합니다.

* Kusto 위에 빌드된 응용 프로그램에서 사용할 수 있는 [패턴 문으로](./patternstatement.md)쿼리 언어를 사용자에게 노출하여 쿼리 이름 확인 프로세스에 자신을 삽입할 수 있습니다.

* Kusto 위에 빌드된 응용 프로그램에서 사출 공격으로부터 자신을 보호하는 쿼리 [매개 변수 문(명령](./queryparametersstatement.md)매개 변수가 SQL 주입 공격으로부터 SQL을 보호하는 방법과 유사).

* Kusto 위에 빌드된 응용 프로그램에서 Kusto의 특정 데이터 하위 집합으로 쿼리를 제한하는 데 사용되는 [제한 문입니다(특정](./restrictstatement.md)열 및 레코드에 대한 액세스 제한 포함).

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
