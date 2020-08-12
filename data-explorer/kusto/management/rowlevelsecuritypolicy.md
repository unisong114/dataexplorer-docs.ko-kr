---
title: 행 수준 보안 (미리 보기)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 행 수준 보안 (미리 보기)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: a82c4b48358a90460f917f181b73b718f6c5e455
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88148118"
---
# <a name="row-level-security-preview"></a>행 수준 보안 (미리 보기)

그룹 멤버 자격 또는 실행 컨텍스트를 사용 하 여 데이터베이스 테이블의 행에 대 한 액세스를 제어 합니다.

RLS (행 수준 보안)는 보안의 디자인과 코딩을 간소화 합니다. 이를 통해 응용 프로그램에서 데이터 행 액세스에 대 한 제한을 적용할 수 있습니다. 예를 들어 사용자의 부서와 관련 된 행에 대 한 액세스를 제한 하거나 회사와 관련 된 데이터에 대해서만 고객 액세스를 제한 합니다.

액세스 제한 논리는 다른 응용 프로그램 계층의 데이터를 벗어나지 않고 데이터베이스 계층에 있습니다. 데이터베이스 시스템은 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용 합니다. 이 논리는 보안 시스템의 노출 영역을 줄임으로써 보안 시스템을 보다 안정적이 고 강력 하 게 만듭니다.

RLS를 사용 하면 테이블의 특정 부분에만 다른 응용 프로그램 및 사용자에 게 액세스 권한을 제공할 수 있습니다. 예를 들어, 다음을 수행합니다.

* 일부 조건을 충족 하는 행에만 액세스 권한 부여
* 일부 열에 데이터 익명화
* 위 항목 모두

자세한 내용은 [행 수준 보안 정책을 관리 하는 명령 제어](../management/row-level-security-policy.md)를 참조 하세요.

> [!NOTE]
> 프로덕션 데이터베이스에서 구성 하는 RLS 정책은 종동체 데이터베이스에도 적용 됩니다. 프로덕션 및 종동체 데이터베이스에서 다른 RLS 정책을 구성할 수 없습니다.

> [!TIP]
> 이러한 함수는 쿼리를 row_level_security 하는 데 유용한 경우가 많습니다.
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

## <a name="limitations"></a>제한 사항

행 수준 보안 정책을 구성할 수 있는 테이블의 수에는 제한이 없습니다.

테이블에 RLS 정책을 사용 하도록 설정할 수 없습니다.
* [연속 데이터 내보내기가](../management/data-export/continuous-data-export.md) 구성 됩니다.
* [업데이트 정책](./updatepolicy.md)쿼리에서 참조 됩니다.
* 제한 된 [보기 액세스 정책을](./restrictedviewaccesspolicy.md) 구성 합니다.

## <a name="examples"></a>예

### <a name="limit-access-to-sales-table"></a>Sales 테이블에 대 한 액세스 제한

이라는 테이블에서 `Sales` 각 행은 판매에 대 한 세부 정보를 포함 합니다. 열 중 하나에는 영업 사원의 이름이 포함 됩니다. 직원에 게의 모든 레코드에 대 한 액세스 권한을 부여 하는 대신 `Sales` 이 테이블에 대 한 행 수준 보안 정책을 사용 하 여 판매원이 현재 사용자 인 레코드만 반환 하도록 합니다.

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

신용 카드 번호를 마스킹할 수도 있습니다.

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

모든 영업 담당자가 특정 국가의 판매를 모두 볼 수 있도록 하려면 다음과 유사한 쿼리를 정의할 수 있습니다.

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

관리자가 포함 된 그룹이 있는 경우 모든 행에 대 한 액세스 권한을 부여 하는 것이 좋습니다. 행 수준 보안 정책을 쿼리 합니다.

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="expose-different-data-to-members-of-different-azure-ad-groups"></a>다른 Azure AD 그룹의 구성원에 게 서로 다른 데이터 노출

여러 Azure AD 그룹이 있는 경우 각 그룹의 구성원이 서로 다른 데이터 하위 집합을 볼 수 있도록 하려면 RLS 쿼리에이 구조를 사용 합니다. 사용자가 단일 Azure AD 그룹에만 속할 수 있다고 가정 합니다.

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="apply-the-same-rls-function-on-multiple-tables"></a>여러 테이블에 동일한 RLS 함수 적용

먼저 테이블 이름을 문자열 매개 변수로 받는 함수를 정의 하 고 연산자를 사용 하 여 테이블을 참조 `table()` 합니다. 

다음은 그 예입니다. 

```
.create-or-alter function RLSForCustomersTables(TableName: string) {
    table(TableName)
    | ...
}
```

그런 다음 이러한 방식으로 여러 테이블에서 RLS를 구성 합니다.


```
.alter table Customers1 policy row_level_security enable "RLSForCustomersTables('Customers1')"
.alter table Customers2 policy row_level_security enable "RLSForCustomersTables('Customers2')"
.alter table Customers3 policy row_level_security enable "RLSForCustomersTables('Customers3')"
```

### <a name="produce-an-error-upon-unauthorized-access"></a>무단 액세스 시 오류 생성

권한이 없는 테이블 사용자가 빈 테이블을 반환 하는 대신 오류를 받도록 하려면 함수를 사용 [`assert()`](../query/assert-function.md) 합니다. 다음 예에서는 RLS 함수에서이 오류를 생성 하는 방법을 보여 줍니다.

```
.create-or-alter function RLSForCustomersTables() {
    MyTable
    | where assert(current_principal_is_member_of('aadgroup=mygroup@mycompany.com') == true, "You don't have access")
}
```

이 접근 방식을 다른 예제와 결합할 수 있습니다. 예를 들어 다른 AAD 그룹의 사용자에 게 다른 결과를 표시 하 고 다른 모든 사용자에 게 오류를 생성할 수 있습니다.

## <a name="more-use-cases"></a>추가 사용 사례

* 콜 센터 지원 담당자는 사회 보장 번호 또는 신용 카드 번호의 여러 자릿수로 호출자를 식별할 수 있습니다. 이러한 숫자는 지원 담당자에 게 완전히 노출 되어서는 안 됩니다. 테이블에 RLS 정책을 적용 하 여 모든 쿼리의 결과 집합에서 사회 보장 또는 신용 카드 번호의 마지막 4 자리를 제외한 모든 것을 마스킹할 수 있습니다.
* PII (개인적으로 식별이 가능한 정보)를 마스크 하는 RLS 정책을 설정 하면 개발자는 규정 준수 규정을 위반 하지 않고 문제 해결을 위해 프로덕션 환경을 쿼리할 수 있습니다.
* 병원은 간호사에서 환자에 대 한 데이터 행만 볼 수 있도록 하는 RLS 정책을 설정할 수 있습니다.
* 뱅크는 직원의 비즈니스 부서 또는 역할을 기준으로 하는 재무 데이터 행에 대 한 액세스를 제한 하는 RLS 정책을 설정할 수 있습니다.
* 다중 테 넌 트 응용 프로그램은 여러 테 넌 트의 데이터를 단일 tableset (효율적)로 저장할 수 있습니다. RLS 정책을 사용 하 여 다른 모든 테 넌 트의 행에서 각 테 넌 트의 데이터 행을 논리적으로 분리 하므로 각 테 넌 트가 해당 데이터 행만 볼 수 있습니다.

## <a name="performance-impact-on-queries"></a>쿼리에 대 한 성능 영향

테이블에 RLS 정책을 사용 하도록 설정 하면 해당 테이블에 액세스 하는 쿼리에 약간의 성능 영향을 줄 수 있습니다. 테이블에 대 한 액세스는 실제로 해당 테이블에 정의 된 RLS 쿼리로 바뀝니다. RLS 쿼리의 성능 영향은 일반적으로 다음과 같은 두 부분으로 구성 됩니다.

* Azure Active Directory에서 구성원 자격 확인
* 데이터에 적용 되는 필터입니다.

다음은 그 예입니다. 

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

사용자가의 일부가 아니면는 *some_group@domain.com* `IsRestrictedUser` 로 평가 됩니다 `false` . 평가 되는 쿼리는 다음과 유사 합니다.

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

마찬가지로가로 계산 되는 경우 `IsRestrictedUser` `true` 에 대 한 쿼리만 `PartialData` 평가 됩니다.

### <a name="improve-query-performance-when-rls-is-used"></a>RLS를 사용 하는 경우 쿼리 성능 향상

* 필터가 높은 카디널리티 열 (예: DeviceID)에 적용 되는 경우 [분할 정책](./partitioningpolicy.md) 또는 [행 순서 정책을](./roworderpolicy.md) 사용 하는 것이 좋습니다.
* 필터가 낮은 중간 수준 카디널리티 열에 적용 되는 경우 [행 순서 정책을](./roworderpolicy.md) 사용 하는 것이 좋습니다.

## <a name="performance-impact-on-ingestion"></a>수집에 대 한 성능 영향

수집 시 성능에 영향을 주지 않습니다.
