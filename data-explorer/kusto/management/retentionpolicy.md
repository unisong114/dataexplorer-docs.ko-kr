---
title: 보존 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 보존 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 81e08b6e007a6e3c669e7138e1d36ae1e701d442
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520318"
---
# <a name="retention-policy"></a>보존 정책

보존 정책은 테이블에서 데이터가 자동으로 제거되는 메커니즘을 제어합니다.
이러한 제거는 일반적으로 관련성이 연령을 기반으로 하는 테이블로 계속 흐르는 데이터에 유용합니다. 예를 들어 보존 정책을 2주 후에 분리해제될 수 있는 진단 이벤트를 포함하는 테이블에 사용할 수 있습니다.

보존 정책은 특정 테이블 또는 전체 데이터베이스에 대해 구성할 수 있습니다(이 경우 정책을 재정의하지 않는 데이터베이스의 모든 테이블에 적용).

데이터를 지속적으로 수집하는 클러스터에서는 보존 정책을 설정하는 것이 중요하며, 이로 인해 비용이 제한됩니다.

보존 정책이 "외부"인 데이터는 삭제할 수 있습니다. Kusto는 제거가 발생하는 시기를 보장하지 않습니다(보존 정책이 트리거된 경우에도 데이터가 "남아 있을 수 있습니다.").

보존 정책은 수집 이후 데이터의 수기간을 제한하도록 가장 일반적으로 [설정됩니다(아래 SoftDeletePeriod](#the-policy-object)참조).

> [!NOTE]
> * 삭제 시간이 정확하지 않습니다. 시스템은 제한을 초과하기 전에 데이터가 삭제되지 않도록 보장하지만 삭제는 해당 시점 바로 다음에 삭제되지 않습니다.
> * 소프트 삭제 기간은 테이블 수준 보존 정책의 일부로 설정할 수 있지만 데이터베이스 수준 보존 정책의 일부로 설정되지는 않습니다.
>   * 이 작업이 완료되면 수집된 데이터가 원본 테이블에 커밋되지 않아 데이터를 유지하지 않아도 됩니다.
>   * 이러한 구성은 주로 데이터가 테이블로 수집될 때 유용합니다.
>   트랜잭션 업데이트 정책은 트랜잭션 [업데이트 정책을](updatepolicy.md) 변환하고 출력을 다른 테이블로 리디렉션하는 데 사용됩니다.

## <a name="the-policy-object"></a>정책 개체

보존 정책에는 다음 속성이 포함됩니다.

* **소프트 삭제 기간**:
    * 데이터를 수집한 시간 이후 측정된 쿼리에 사용할 수 있도록 유지되도록 보장되는 시간 범위입니다.
    * 기본값은 `100 years`입니다.
    * 테이블 또는 데이터베이스의 소프트 삭제 기간을 변경할 때 새 값은 기존 데이터와 새 데이터 모두에 적용됩니다.
* **복구 가능성**:
    * 데이터가 삭제된 후 데이터 복구 가능성(사용 안 함/사용 안 함)
    * 기본값은 `enabled`입니다.
    * 로 설정된 `enabled`경우 삭제 후 14일 동안 데이터를 복구할 수 있습니다.

## <a name="control-commands"></a>제어 명령

* [.show 정책 보존을](../management/retention-policy.md) 사용하여 데이터베이스 또는 테이블에 대한 현재 보존 정책을 표시합니다.
* [.alter 정책 보존을](../management/retention-policy.md) 사용하여 데이터베이스 또는 테이블의 현재 보존 정책을 변경합니다.

## <a name="defaults"></a>기본값

기본적으로 데이터베이스 또는 테이블을 만들 때 보존 정책이 정의되어 있지 않습니다.
일반적인 경우 데이터베이스가 생성된 다음 알려진 요구 사항에 따라 작성자가 설정한 보존 정책을 즉시 설정합니다.
정책 집합이 없는 데이터베이스 또는 테이블의 보존 정책에 대한 [show](../management/retention-policy.md) 명령을 `Policy` 실행할 `null`때 로 나타납니다.

위에서 언급한 기본값으로 기본 보존 정책을 다음 명령을 사용하여 적용할 수 있습니다.

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

이러한 결과는 데이터베이스 또는 테이블에 다음 정책 개체가 적용됩니다.

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

데이터베이스 또는 테이블의 보존 정책을 지우는 작업은 다음 명령을 사용하여 수행할 수 있습니다.

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>예

클러스터에 테이블이 있는 `MyDatabase` `MyTable1` `MyTable2` 데이터베이스가 있고`MySpecialTable`

**1. 데이터베이스의 모든 테이블을 7 일의 소프트 삭제 기간과 비활성화 된 복구 가능성을 갖도록 설정하십시오.**

* *옵션 1(권장)*: 7일의 일시 삭제 기간과 복구 기능을 사용하지 않도록 설정한 데이터베이스 수준 보존 정책을 설정하고 테이블 수준 정책이 설정되어 있지 않은지 확인합니다.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *옵션 2*: 각 테이블에 대해 7일의 소프트 삭제 기간과 복구 가능성을 사용하지 않도록 설정하여 테이블 수준 보존 정책을 설정합니다.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. 테이블 `MyTable1` `MyTable2` 설정, 7 일의 소프트 삭제 기간 및 복구 `MySpecialTable` 가능을 활성화하고, 14 일의 소프트 삭제 기간 및 복구 기능을 사용하지 않도록 설정**:

* *옵션 1(권장)*: 7일의 소프트 삭제 기간과 복구 가능이 활성화된 데이터베이스 수준 보존 정책을 설정하고, 에 대해 `MySpecialTable`소프트 삭제 기간 14일 및 복구 가능성을 사용하지 않도록 설정한 테이블 수준 보존 정책을 설정합니다.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *옵션 2*: 각 테이블에 대해 원하는 일시 삭제 기간 및 복구 가능성으로 테이블 수준 보존 정책을 설정합니다.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. 테이블 `MyTable1` `MyTable2` 설정, 7 일의 소프트 삭제 기간을 `MySpecialTable` 가지고, 무기한 데이터를 유지하려면**:

* *옵션 1*: 7일의 소프트 삭제 기간을 사용하여 데이터베이스 수준 보존 정책을 설정하고 에 대해 `MySpecialTable`100년(기본 보존 정책)의 소프트 삭제 기간으로 테이블 수준 보존 정책을 설정합니다.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *옵션 2*: `MyTable1` `MyTable2`테이블의 경우 원하는 일시 삭제 기간을 7일로 테이블 수준 보존 정책을 설정하고 데이터베이스 수준 및 `MySpecialTable` 테이블 수준 정책이 설정되지 않았는지 확인합니다.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *옵션 3*: `MyTable1` `MyTable2`테이블의 경우 원하는 일시 삭제 기간을 7일로 테이블 수준 보존 정책을 설정합니다. 테이블의 `MySpecialTable`경우 100년의 소프트 삭제 기간(기본 보존 정책)으로 테이블 수준 보존 정책을 설정합니다.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```