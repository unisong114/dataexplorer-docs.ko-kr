---
title: RowLevelSecurity 정책-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 RowLevelSecurity 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/11/2020
ms.openlocfilehash: f73cf5718a80528415c9aed201917c1bd52bb660
ms.sourcegitcommit: 86636f80a12f47ea434f128fa04fe9fc09629730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91942636"
---
# <a name="row_level_security-policy-command"></a>row_level_security 정책 명령

이 문서에서는 데이터베이스 테이블에 대 한 [row_level_security 정책을](rowlevelsecuritypolicy.md) 구성 하는 데 사용 되는 명령을 설명 합니다.

## <a name="displaying-the-policy"></a>정책 표시

정책을 표시 하려면 다음 명령을 사용 합니다.

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>정책 구성

테이블에 대 한 row_level_security 정책 사용:

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

테이블에 대 한 row_level_security 정책 사용 안 함:

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

정책을 사용 하지 않도록 설정한 경우에도 단일 쿼리에 적용 되도록 강제할 수 있습니다. 쿼리 앞에 다음 줄을 입력 합니다.

`set query_force_row_level_security;`

Row_level_security에 대 한 다양 한 쿼리를 시도 하지만 실제로 사용자에 게는 적용 하지 않으려는 경우에 유용 합니다. 쿼리에서 확신 하는 경우 정책을 사용 하도록 설정 합니다.

> [!NOTE]
> 에는 다음과 같은 제한 사항이 적용 됩니다 `query` .
>
> * 쿼리가 정책이 정의 된 테이블과 정확히 동일한 스키마를 생성 해야 합니다. 즉, 쿼리 결과는 동일한 이름 및 형식을 사용 하 여 원래 테이블과 동일한 열을 동일한 순서로 반환 해야 합니다.
> * 쿼리에서는 `extend` , `where` ,,,, `project` `project-away` `project-rename` `project-reorder` `join` 및 `union` 연산자를 사용할 수 있습니다.
> * 이 쿼리는 RLS를 사용 하는 다른 테이블을 참조할 수 없습니다.
> * 쿼리는 다음 중 하나 또는 조합 중 하나일 수 있습니다.
>    * 쿼리 (예: `<table_name> | extend CreditCardNumber = "****"` )
>    * 함수 (예: `AnonymizeSensitiveData` )
>    * Datatable (예: `datatable(Col1:datetime, Col2:string) [...]` )

> [!TIP]
> 이러한 함수는 쿼리를 row_level_security 하는 데 유용한 경우가 많습니다.
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

**성능 정보**: `UserCanSeeFullNumbers` 먼저 평가한 다음 또는 중 하나를 `AllData` `PartialData` 평가 하지만 둘 중 하나는 예상 된 결과입니다.
RLS의 성능 영향에 대 한 자세한 내용은 [여기](rowlevelsecuritypolicy.md#performance-impact-on-queries)를 참조 하세요.

## <a name="deleting-the-policy"></a>정책을 삭제 하는 중

```kusto
.delete table <table_name> policy row_level_security
```
