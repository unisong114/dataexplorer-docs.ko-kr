---
title: current_principal_is_member_of ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 current_principal_is_member_of ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 87742fd0e7678ecdc3441e0eb25c9b9acbb56804
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252505"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

쿼리를 실행 하는 현재 보안 주체의 그룹 멤버 자격 또는 보안 주체 id를 확인 합니다.

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

## <a name="syntax"></a>구문

`current_principal_is_member_of`(`*list of string literals*`)

## <a name="arguments"></a>인수

* *식 목록* -쉼표로 구분 된 문자열 리터럴의 목록이 며 각 리터럴은 다음과 같이 형성 된 FQN (주 정규화 된 이름) 문자열입니다.  
*PrinciplaType* `=` *Principalid* `;` *TenantId*

| PrincipalType   | FQN 접두사  |
|-----------------|-------------|
| AAD 사용자        | `aaduser=`  |
| AAD 그룹       | `aadgroup=` |
| AAD 응용 프로그램 | `aadapp=`   |

## <a name="returns"></a>반환
  
함수에서 다음을 반환합니다.
* `true`: 쿼리를 실행 하는 현재 보안 주체가 하나 이상의 입력 인수와 일치 하는 경우
* `false`: 쿼리를 실행 하는 현재 보안 주체가 FQN 인수의 멤버가 아니고 `aadgroup=` `aaduser=` 또는 `aadapp=` FQN 인수와 같지 않은 경우
* `(null)`: 쿼리를 실행 하는 현재 보안 주체가 FQN 인수의 멤버가 아니고 `aadgroup=` `aaduser=` 또는 `aadapp=` FQN 인수와 같지 않고 하나 이상의 FQN 인수가 성공적으로 확인 되지 않은 경우 (Azure AD에서 누르지 않음) 

> [!NOTE]
> 함수는 세 번째 상태 값 (, 및)을 반환 하기 때문에 `true` `false` `null` 성공적인 멤버 자격을 확인 하기 위해 긍정 반환 값만 사용 하는 것이 중요 합니다. 즉, 다음 식은 동일 하지 않습니다.
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| result |
|--------|
| (null) |

여러 인수 대신 동적 배열을 사용 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| result |
|--------|
| (null) |

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
