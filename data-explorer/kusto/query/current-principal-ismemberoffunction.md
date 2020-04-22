---
title: current_principal_is_member_of() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 current_principal_is_member_of()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6d33fd19181e1eba93b54f684864fed062701307
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663853"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

쿼리를 실행하는 현재 보안 주체의 그룹 구성원 자격 또는 보안 주체 ID를 확인합니다.

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

**구문**

`current_principal_is_member_of`(`*list of string literals*`)

**인수**

* *식 목록* - 각 리터럴이 다음과 같이 형성된 주 정규 정규 화(FQN) 문자열인 문자열 리터럴의 쉼표 분리 목록입니다.  
*프린시플라타입*`=`*PrincipalId*`;`*테넌드*

| PrincipalType   | FQN 접두사  |
|-----------------|-------------|
| AAD 사용자        | `aaduser=`  |
| AAD 그룹       | `aadgroup=` |
| AAD 응용 프로그램 | `aadapp=`   |

**반환**

함수에서 다음을 반환합니다.
* `true`: 쿼리를 실행하는 현재 주체가 하나 이상의 입력 인수에 대해 성공적으로 일치하는 경우.
* `false`: 쿼리를 실행하는 현재 주 체가 `aadgroup=` FQN 인수의 멤버가 아니며 `aaduser=` 또는 `aadapp=` FQN 인수와 같지 않은 경우.
* `(null)`: 쿼리를 실행하는 현재 주 체가 `aadgroup=` FQN 인수의 멤버가 아니고 `aaduser=` `aadapp=` 또는 FQN 인수와 같지 않고 하나 이상의 FQN 인수가 성공적으로 해결되지 않은 경우(AAD에서 미리 지정되지 않음). 

> [!NOTE]
> 함수는 트라이 상태 값`true` `false` `null`(및) 반환 하기 때문에 성공적인 멤버 자격을 확인 하려면 양수 반환 값에만 의존 하는 것이 중요 합니다. 즉, 다음 표현식은 동일하지 않습니다.
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**예제**

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

multple 인수 대신 동적 배열 사용:

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

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end