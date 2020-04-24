---
title: 행 수준 보안 (미리 보기) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 행 수준 보안(미리 보기)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 548b0e9e40cfd709b40e548fe7e0a8ad42aa4abc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520284"
---
# <a name="row-level-security-preview"></a>행 수준 보안(미리 보기)

그룹 구성원 자격 또는 실행 컨텍스트를 사용하여 데이터베이스 테이블의 행에 대한 액세스를 제어합니다.

RLS(행 수준 보안)는 데이터 행 액세스에 대한 제한을 적용할 수 있도록 하여 응용 프로그램의 보안 설계 및 코딩을 단순화합니다. 예를 들어 해당 부서와 관련된 행에 대한 사용자 액세스를 제한하거나 고객 액세스를 회사와 관련된 데이터로만 제한할 수 있습니다.

액세스 제한 논리는 다른 응용 프로그램 계층의 데이터에서 벗어나는 것이 아니라 데이터베이스 계층에 있습니다. 데이터베이스 시스템은 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용합니다. 이렇게 하면 보안 시스템의 노출 영역을 줄임으로써 보안 시스템을 보다 안정적이고 강력하게 만들 수 있습니다.

RLS를 사용하면 테이블의 특정 부분에만 다른 응용 프로그램 및/또는 사용자에 대한 액세스를 제공할 수 있습니다. 예를 들면 다음과 같습니다.

* 일부 기준을 충족하는 행에만 액세스 권한 부여
* 일부 열의 데이터 익명화
* 위의 둘 다

> [!NOTE]
> 행 수준 보안 정책은 테이블에서 사용할 수 없습니다.
> * 연속 [데이터 내보내기가](../management/data-export/continuous-data-export.md) 구성된 경우
> * 이는 일부 [업데이트 정책](./updatepolicy.md)의 쿼리에서 참조합니다.
> * 제한된 [보기 액세스 정책이](./restrictedviewaccesspolicy.md) 구성된 경우

행 수준 보안 정책을 관리하기 위한 제어 명령에 대한 자세한 내용은 [여기를 참조하십시오.](../management/row-level-security-policy.md)

## <a name="examples"></a>예

라는 `Sales`테이블에서 각 행에는 판매에 대한 세부 정보가 포함됩니다. 열 중 하나에는 영업 사원의 이름이 포함됩니다.
영업 담당자에게 `Sales`에서 의 모든 레코드에 대한 액세스 권한을 부여하는 대신 이 테이블에서 행 수준 보안 정책을 사용하도록 설정하여 영업 담당자가 현재 사용자인 레코드만 반환할 수 있습니다.

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

신용 카드 번호를 마스킹할 수도 있습니다.

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

모든 영업 사원이 특정 국가의 모든 판매를 보도록 하려면 다음과 유사한 쿼리를 정의할 수 있습니다.

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

영업 사원의 관리자가 포함된 AAD 그룹이 있는 경우 영업 담당자가 모든 행에 액세스할 수 있도록 할 수 있습니다. 행 수준 보안 정책의 다음 쿼리를 통해 이 작업을 수행할 수 있습니다.

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

일반적으로 여러 AAD 그룹이 있고 각 그룹의 구성원이 다른 데이터 하위 집합을 표시하려는 경우 RLS 쿼리에 대해 이 구조를 따를 수 있습니다(사용자가 단일 AAD 그룹에만 속할 수 있다고 가정).

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

## <a name="more-use-cases"></a>더 많은 사용 사례

* 콜 센터 지원 담당자는 주민 등록 번호 또는 신용 카드 번호의 여러 자리로 발신자를 식별할 수 있습니다. 이러한 번호는 지원 담당자에게 완전히 노출되어서는 안 됩니다. 모든 쿼리의 결과 집합에서 사회 보장 또는 신용 카드 번호의 마지막 네 자리를 제외한 모든 것을 마스킹하기 위해 테이블에 RLS 정책을 적용할 수 있습니다.
* 개인 식별 정보(PII)를 마스킹하는 RLS 정책을 설정하여 개발자가 규정 준수 규정을 위반하지 않고 문제 해결을 위해 프로덕션 환경을 쿼리할 수 있도록 합니다.
* 병원은 간호사가 환자에 대한 데이터 행만 볼 수 있도록 하는 RLS 정책을 설정할 수 있습니다.
* 은행은 직원의 사업부 또는 역할에 따라 재무 데이터 행에 대한 액세스를 제한하는 RLS 정책을 설정할 수 있습니다.
* 다중 테넌트 응용 프로그램은 여러 테넌트의 데이터를 단일 테이블집합에 저장할 수 있습니다(매우 효율적임). RLS 정책을 사용하여 각 테넌트의 데이터 행을 다른 모든 테넌트의 행에서 논리적으로 분리하여 각 테넌트행만 볼 수 있습니다.

## <a name="performance-impact-on-queries"></a>쿼리에 대한 성능 영향

테이블에서 RLS 정책을 사용하도록 설정하면 해당 테이블에 액세스하는 쿼리에 성능에 일부 영향이 있습니다. 테이블에 대한 액세스는 실제로 해당 테이블에 정의된 RLS 쿼리로 대체됩니다. RLS 쿼리의 성능 영향은 일반적으로 다음 두 부분으로 구성됩니다.

* Azure Active 디렉터리에서 멤버 자격 검사
* 데이터에 적용된 필터

다음은 그 예입니다.

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

사용자가 some_group@domain.com에 `IsRestrictedUser` 속하지 않은 경우 `false`을 평가하므로 평가할 쿼리는 다음과 유사합니다.

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

마찬가지로 `IsRestrictedUser` `true`을 평가하는 경우 에 대한 `PartialData` 쿼리만 평가됩니다.

## <a name="performance-impact-on-ingestion"></a>섭취에 대한 성능 영향

없음