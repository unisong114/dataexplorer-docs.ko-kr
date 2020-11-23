---
title: 보안 역할 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 보안 역할 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3b30a3e578b6bb1f21dedfcfec7629b60bb255f3
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324502"
---
# <a name="security-roles-management"></a>보안 역할 관리

> [!IMPORTANT]
> Kusto 클러스터에서 권한 부여 규칙을 변경 하기 전에 다음을 읽어 보세요. [액세스 제어 개요](../management/access-control/index.md)  
>  [역할 기반 권한 부여](../management/access-control/role-based-authorization.md) 

이 문서에서는 보안 역할을 관리 하는 데 사용 되는 제어 명령을 설명 합니다.
보안 역할은 데이터베이스 또는 테이블과 같은 보안 리소스에 대해 작업을 수행할 권한이 있는 보안 주체 (사용자 및 응용 프로그램) 및 허용 되는 작업을 정의 합니다. 예를 들어 `database viewer` 특정 데이터베이스에 대 한 보안 역할이 있는 보안 주체는 제한 된 테이블을 제외 하 고 해당 데이터베이스의 모든 엔터티를 쿼리하고 볼 수 있습니다.

보안 주체 또는 보안 그룹 (다른 보안 주체 또는 다른 보안 그룹을 가질 수 있음)에 보안 역할을 연결할 수 있습니다. 보안 주체가 보안 리소스에 대 한 작업을 수행 하려고 하면 시스템은 보안 주체가 리소스에 대해이 작업을 수행할 수 있는 권한을 부여 하는 하나 이상의 보안 역할에 연결 되어 있는지 확인 합니다. 이를 **권한 부여 확인** 이라고 합니다. 권한 부여 확인이 실패 하면 작업이 중단 됩니다.

**구문**

보안 역할 관리 명령의 구문:

*Verb* *SecurableObjectType* *SecurableObjectName* *Role* [ `(` *listofprincipals* `)` [*Description*]]

* *동사* 는 수행할 작업의 종류를 나타냅니다.,, `.show` `.add` `.drop` 및 `.set`

    |*동사* |Description                                  |
    |-------|---------------------------------------------|
    |`.show`|현재 값을 반환 합니다.         |
    |`.add` |역할에 하나 이상의 보안 주체를 추가 합니다.     |
    |`.drop`|역할에서 하나 이상의 보안 주체를 제거 합니다.|
    |`.set` |역할을 특정 보안 주체 목록으로 설정 하 고 이전 모든 항목을 제거 합니다 (있는 경우).|

* *SecurableObjectType* 는 역할이 지정 된 개체의 종류입니다.

    |*SecurableObjectType*|Description|
    |---------------------|-----------|
    |`database`|지정 된 데이터베이스|
    |`table`|지정 된 테이블|
    |`materialized-view`| 지정 된 [구체화 된 뷰입니다](materialized-views/materialized-view-overview.md) .| 

* *SecurableObjectName* 는 개체의 이름입니다.

* *Role* 은 관련 역할의 이름입니다.

    |*역할*      |Description|
    |------------|-----------|
    |`principals`|는 동사의 일부로만 나타날 수 있으며 `.show` 보안 가능한 개체에 영향을 줄 수 있는 보안 주체의 목록을 반환 합니다.|
    |`admins`    |개체 및 모든 하위 개체를 보고 수정 하 고 제거 하는 기능을 포함 하 여 보안 개체에 대 한 제어 권한을 가집니다.|
    |`users`     |보안 개체를 확인 하 고 그 아래에 새 개체를 만들 수 있습니다.|
    |`viewers`   |보안 개체를 볼 수 있습니다.|
    |`unrestrictedviewers`|데이터베이스 수준 에서만은 제한 된 테이블 ("normal" 및에 노출 되지 않음)을 볼 수 `viewers` 있습니다 `users` .|
    |`ingestors` |데이터베이스 수준 에서만 모든 테이블에 대 한 데이터 수집을 허용 합니다.|
    |`monitors`  ||

* *Listofprincipals* 은 선택적으로 쉼표로 구분 된 보안 주체 식별자 (형식의 값) 목록입니다 `string` .

* *Description* 은 `string` 나중에 감사를 위해 연결과 함께 저장 되는 형식의 선택적 값입니다.

## <a name="example"></a>예제

다음 제어 명령은 데이터베이스의 테이블에 대 한 일부 액세스 권한이 있는 모든 보안 주체를 나열 합니다 `StormEvents` .

```kusto
.show table StormEvents principals
```

다음은이 명령의 잠재적 결과입니다.

|역할 |PrincipalType |PrincipalDisplayName |PrincipalObjectId |PrincipalFQN 
|---|---|---|---|---
|데이터베이스 Apsty 관리자 |Azure AD 사용자 |표시 Smith |cd709aed-a26c-e3953dec735e |aaduser =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>데이터베이스 보안 역할 관리

`.set``database` *DatabaseName* *역할* `none` [ `skip-results` ]

`.set``database` *DatabaseName* *역할* `(` *보안 주체* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``database` *DatabaseName* *역할* `(` *보안 주체* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``database` *DatabaseName* *역할* `(` *보안 주체* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

첫 번째 명령은 역할에서 모든 보안 주체를 제거 합니다. 두 번째는 역할에서 모든 보안 주체를 제거 하 고 새 보안 주체 집합을 설정 합니다. 세 번째는 기존 보안 주체를 제거 하지 않고 역할에 새 보안 주체를 추가 합니다. 마지막은 역할에서 표시 된 보안 주체를 제거 하 고 나머지는 유지 합니다.

위치:

* *DatabaseName* 은 보안 역할이 수정 되 고 있는 데이터베이스의 이름입니다.

* *Role* 은 `admins` , `ingestors` ,, `monitors` , `unrestrictedviewers` `users` 또는 `viewers` 입니다.

* *보안 주체* 는 하나 이상의 보안 주체입니다. 이러한 보안 주체를 지정 하는 방법에 대해서는 [보안 주체 및 id 공급자](./access-control/principals-and-identity-providers.md) 를 참조 하세요.

* `skip-results`(제공 된 경우)는 명령이 업데이트 된 데이터베이스 보안 주체 목록을 반환 하지 않도록 요청 합니다.

* *설명*(제공 된 경우)은 변경 내용과 연결 되며 해당 명령에 의해 검색 되는 텍스트입니다 `.show` .

## <a name="managing-table-security-roles"></a>테이블 보안 역할 관리

`.set``table` *TableName* *Role* `none` [ `skip-results` ]

`.set``table` *TableName* *Role* `(` *principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``table` *TableName* *Role* `(` *principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``table` *TableName* *Role* `(` *principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

첫 번째 명령은 역할에서 모든 보안 주체를 제거 합니다. 두 번째는 역할에서 모든 보안 주체를 제거 하 고 새 보안 주체 집합을 설정 합니다. 세 번째는 기존 보안 주체를 제거 하지 않고 역할에 새 보안 주체를 추가 합니다. 마지막은 역할에서 표시 된 보안 주체를 제거 하 고 나머지는 유지 합니다.

위치:

* *TableName* 은 보안 역할이 수정 되 고 있는 테이블의 이름입니다.

* *Role* 은 `admins` 또는 `ingestors` 입니다.

* *보안 주체* 는 하나 이상의 보안 주체입니다. 이러한 보안 주체를 지정 하는 방법에 대해서는 [보안 주체 및 id 공급자](./access-control/principals-and-identity-providers.md) 를 참조 하세요.

* `skip-results`제공 되는 경우 명령에서 테이블 보안 주체의 업데이트 된 목록을 반환 하지 않도록 요청 합니다.

* *설명*(제공 된 경우)은 변경 내용과 연결 되며 해당 명령에 의해 검색 되는 텍스트입니다 `.show` .

**예제**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-materialized-view-security-roles"></a>구체화 된 뷰 보안 역할 관리

`.show``materialized-view` *MaterializedViewName*`principals`

`.set``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(` *보안 주체* `,[` *주* ...`])`

`.add``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(` *보안 주체* `,[` *주* ...`])`

`.drop``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(` *보안 주체* `,[` *주* ...`])`

위치:

* *MaterializedViewName* 은 보안 역할이 수정 되 고 있는 구체화 된 뷰의 이름입니다.
* *보안 주체* 는 하나 이상의 보안 주체입니다. [보안 주체 및 id 공급자를](./access-control/principals-and-identity-providers.md) 참조 하세요.

## <a name="managing-function-security-roles"></a>함수 보안 역할 관리

`.set``function` *FunctionName* *Role* `none` [ `skip-results` ]

`.set``function` *FunctionName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``function` *FunctionName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``function` *FunctionName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

첫 번째 명령은 역할에서 모든 보안 주체를 제거 합니다. 두 번째는 역할에서 모든 보안 주체를 제거 하 고 새 보안 주체 집합을 설정 합니다. 세 번째는 기존 보안 주체를 제거 하지 않고 역할에 새 보안 주체를 추가 합니다. 마지막은 역할에서 표시 된 보안 주체를 제거 하 고 나머지는 유지 합니다.

위치:

* *FunctionName* 는 보안 역할을 수정 하는 함수의 이름입니다.

* *Role* 은 항상 `admin` 입니다.

* *보안 주체* 는 하나 이상의 보안 주체입니다. 이러한 보안 주체를 지정 하는 방법에 대해서는 [보안 주체 및 id 공급자](./access-control/principals-and-identity-providers.md) 를 참조 하세요.

* `skip-results`제공 되는 경우 명령에서 업데이트 된 함수 보안 목록을 반환 하지 않도록 요청 합니다.

* *설명*(제공 된 경우)은 변경 내용과 연결 되며 해당 명령에 의해 검색 되는 텍스트입니다 `.show` .

**예제**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

