---
title: Kusto 보존 정책은 데이터가 제거 되는 방법을 제어 합니다. Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 보존 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 37d82807751a604d88eda7de75fb4978efc0ce1b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321270"
---
# <a name="retention-policy"></a>보존 정책

보존 정책은 테이블 또는 [구체화 된 뷰에서](materialized-views/materialized-view-overview.md)데이터를 자동으로 제거 하는 메커니즘을 제어 합니다. 테이블에 지속적으로 흐르는 데이터를 제거 하는 것이 유용 합니다. 예를 들어 2 주 후에 필요 하지 않은 될 수 있는 진단 이벤트를 포함 하는 테이블에 정책을 사용할 수 있습니다.

특정 테이블이 나 구체화 된 뷰나 전체 데이터베이스에 대해 보존 정책을 구성할 수 있습니다. 그런 다음 정책을 재정의 하지 않는 데이터베이스의 모든 테이블에 적용 됩니다.

보존 정책을 설정 하는 것은 데이터를 지속적으로 수집 하 여 비용을 제한 하는 클러스터에 중요 합니다.

"외부" 데이터는 보존 정책을 제거 하는 데 적합 합니다. 제거를 수행 하는 경우 특정 한 보장은 없습니다. 보존 정책이 트리거되는 경우에도 데이터가 "링거" 할 수 있습니다.

보존 정책은 수집 이후 데이터의 보관 기간을 제한 하기 위해 가장 일반적으로 설정 됩니다. 자세한 내용은 [소프트 Deleteperiod](#the-policy-object)를 참조 하세요.

> [!NOTE]
> * 삭제 시간이 정확 하지 않습니다. 시스템은 한도를 초과 하기 전에 데이터를 삭제 하지 않도록 보장 하지만 해당 지점 다음에는 삭제를 즉시 수행 하지 않습니다.
> * 일시 삭제 기간 0은 테이블 수준 보존 정책의 일부로 설정할 수 있지만 데이터베이스 수준 보존 정책의 일부로는 설정할 수 없습니다.
>   * 이 작업이 완료 되 면 데이터를 유지할 필요가 없도록 수집 데이터가 원본 테이블에 커밋되지 않습니다. 따라서는 `Recoverability` 로만 설정할 수 있습니다 `Disabled` .
>   * 이러한 구성은 데이터를 테이블로 수집 때 주로 유용 합니다.
> 트랜잭션 [업데이트 정책은](updatepolicy.md) 이를 변환 하 고 출력을 다른 테이블로 리디렉션하는 데 사용 됩니다.

## <a name="the-policy-object"></a>정책 개체

보존 정책에는 다음과 같은 속성이 포함 됩니다.

* **소프트 Deleteperiod**:
    * 데이터를 쿼리에 사용할 수 있도록 보장 하는 시간 범위입니다. 기간은 데이터가 수집 된 시간부터 측정 됩니다.
    * 기본값은 `100 years`입니다.
    * 테이블이 나 데이터베이스의 일시 삭제 기간을 변경 하는 경우 새 값은 기존 데이터와 새 데이터에 모두 적용 됩니다.
* **복구**:
    * 데이터를 삭제 한 후 데이터 복구 (사용/사용 안 함)
    * 기본값은 `Enabled`입니다.
    * 로 설정 된 경우 `Enabled` 데이터는 일시 삭제 된 후 14 일 동안 복구 됩니다.

## <a name="control-commands"></a>제어 명령

* [`.show policy retention`](../management/retention-policy.md)데이터베이스, 테이블 또는 [구체화 된 뷰에](materialized-views/materialized-view-overview.md)대 한 현재 보존 정책을 표시 하는 데 사용 합니다.
* 를 사용 [`.alter policy retention`](../management/retention-policy.md) 하 여 데이터베이스, 테이블 또는 [구체화 된 뷰의](materialized-views/materialized-view-overview.md)현재 보존 정책을 변경할 수 있습니다.

## <a name="defaults"></a>기본값

기본적으로 데이터베이스 또는 테이블을 만들 때 보존 정책이 정의 되어 있지 않습니다. 일반적으로 데이터베이스는 생성 된 다음 알려진 요구 사항에 따라 작성자에 의해 즉시 설정 된 보존 정책이 있습니다.
정책을 설정 하지 않은 데이터베이스 또는 테이블의 보존 정책에 대 한 [ `.show` 명령을](../management/retention-policy.md) 실행 하는 경우는 `Policy` 로 표시 됩니다 `null` .

다음 명령을 사용 하 여 위에서 언급 한 기본값을 사용 하 여 기본 보존 정책을 적용할 수 있습니다.

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
.alter materialized-view ViewName policy retention "{}"
```

이 명령은 데이터베이스 또는 테이블에 적용 되는 다음 정책 개체를 반환 합니다.

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

다음 명령을 사용 하 여 데이터베이스 또는 테이블의 보존 정책을 지우는 작업을 수행할 수 있습니다.

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>예제

`MyDatabase`테이블이, `MyTable1` `MyTable2` 및 인 라는 데이터베이스가 있는 클러스터의 경우 `MySpecialTable`

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>일시 삭제 기간 7 일 및 복구 사용 안 함

데이터베이스의 모든 테이블에 대해 일시 삭제 기간을 7 일로 설정 하 고 복구 하지 않도록 설정 합니다.

* *옵션 1 (권장)*: 데이터베이스 수준 보존 정책을 설정 하 고 테이블 수준 정책이 설정 되지 않았는지 확인 합니다.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge materialized-view ViewName policy retention softdelete = 7d 
```

* *옵션 2*: 각 테이블에 대해 일시 삭제 기간이 7 일이 고 복구를 사용 하지 않도록 설정 하 여 테이블 수준 보존 정책을 설정 합니다.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>일시 삭제 기간 (7 일 및 복구 가능)

* 테이블 `MyTable1` 을 설정 `MyTable2` 하 고 일시 삭제 기간을 7 일로 설정 하 고 복구를 사용 하도록 설정 합니다.
* `MySpecialTable`일시 삭제 기간을 14 일로 설정 하 고 복구를 사용 하지 않도록 설정 합니다.

* *옵션 1 (권장)*: 데이터베이스 수준 보존 정책을 설정 하 고 테이블 수준 보존 정책을 설정 합니다.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *옵션 2*: 각 테이블에 대해 적절 한 일시 삭제 기간과 복구를 사용 하 여 테이블 수준 보존 정책을 설정 합니다.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>일시 삭제 기간을 7 일로 유지 하 고 `MySpecialTable` 데이터를 무기한으로 유지 합니다.

테이블을로 설정 `MyTable1` `MyTable2` 하 고 일시 삭제 기간을 7 일로 설정 하 고 `MySpecialTable` 데이터를 무기한으로 유지 합니다.

* *옵션 1*: 데이터베이스 수준 보존 정책을 설정 하 고에 대 한 기본 보존 정책 인 일시 삭제 기간을 100 년으로 설정 하 여 테이블 수준 보존 정책을 설정 `MySpecialTable` 합니다.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *옵션 2*: 테이블 및의 경우 `MyTable1` `MyTable2` 테이블 수준 보존 정책을 설정 하 고에 대 한 데이터베이스 수준 및 테이블 수준 정책이 `MySpecialTable` 설정 되지 않았는지 확인 합니다.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *옵션 3*: 테이블 및의 경우 `MyTable1` `MyTable2` 테이블 수준 보존 정책을 설정 합니다. 테이블의 경우 `MySpecialTable` 기본 보존 정책 인 100 년의 일시 삭제 기간을 사용 하 여 테이블 수준 보존 정책을 설정 합니다.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
