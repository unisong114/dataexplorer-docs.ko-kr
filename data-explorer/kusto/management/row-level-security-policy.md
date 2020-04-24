---
title: RowLevel보안 정책(미리 보기) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 RowLevelSecurity 정책(미리 보기)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: bf8ee8bc4c43c4ed3bcd320ce5b4778f33c3cc64
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520301"
---
# <a name="rowlevelsecurity-policy-preview"></a>로우 레벨 보안 정책(미리 보기)

이 문서에서는 데이터베이스 테이블에 대한 [row_level_security 정책을](rowlevelsecuritypolicy.md) 구성하는 데 사용되는 명령에 대해 설명합니다.

## <a name="displaying-the-policy"></a>정책 표시

정책을 표시하려면 다음 명령을 사용합니다.

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>정책 구성

테이블에서 row_level_security 정책을 사용하도록 설정합니다.

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

테이블에서 row_level_security 정책을 사용하지 않도록 설정합니다.

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

정책이 비활성화된 경우에도 단일 쿼리에 적용하도록 강제할 수 있습니다. 쿼리 앞에 다음 줄을 입력합니다.

`set query_force_row_level_security;`

이 기능은 row_level_security 대한 다양한 쿼리를 시도하려는 경우 유용하지만 실제로 사용자에게 적용되지 않도록 하려는 경우에 유용합니다. 쿼리에 확신이 있으면 정책을 사용하도록 설정합니다.

> [!NOTE]
> 다음 제한 사항은 `query`다음 사항에 적용됩니다.
>
> * 쿼리는 정책이 정의된 테이블과 정확히 동일한 스키마를 생성해야 합니다. 즉, 쿼리 결과는 동일한 이름과 형식을 가진 원래 테이블과 동일한 열을 동일한 순서로 반환해야 합니다.
> * 쿼리는 다음 연산자만 `extend`사용할 `where` `project`수 `project-away` `project-rename` `project-reorder` 있습니다. `union`
> * 쿼리는 정책이 정의된 테이블 이외의 테이블을 참조할 수 없습니다.
> * 쿼리는 다음 중 어느 한 가지 또는 이들의 조합일 수 있습니다.
>    * `<table_name> | extend CreditCardNumber = "****"`쿼리(예:)
>    * 기능(예: `AnonymizeSensitiveData`)
>    * 데이터 테이블(예: `datatable(Col1:datetime, Col2:string) [...]`)

> [!TIP]
> 이러한 함수는 쿼리를 row_level_security 데 유용합니다.
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**예제**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```
**성능**참고 `UserCanSeeFullNumbers` : 먼저 평가된 `AllData` 다음 `PartialData` 평가되거나 평가되지만 둘 다 평가되지는 않습니다.
RLS의 성능 영향에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](rowlevelsecuritypolicy.md#performance-impact-on-queries)

## <a name="deleting-the-policy"></a>정책 삭제

```kusto
.delete table <table_name> policy row_level_security
```