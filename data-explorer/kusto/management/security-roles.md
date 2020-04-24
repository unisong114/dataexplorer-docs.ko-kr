---
title: 보안 역할 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 보안 역할 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea56911ff2ad320d1070da2a4b92d94f060273cf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520097"
---
# <a name="security-roles-management"></a>보안 역할 관리

> [!IMPORTANT]
> Kusto 클러스터의 권한 부여 규칙을 변경하기 전에 [Kusto 액세스 제어 개요](../management/access-control/index.md) 
> [역할 기반 권한 부여를](../management/access-control/role-based-authorization.md) 읽으십시오. 

이 문서에서는 보안 역할을 관리하는 데 사용되는 제어 명령에 대해 설명합니다.
보안 역할은 데이터베이스 나 테이블과 같은 보안 리소스에서 작업할 수 있는 권한이 있는 보안 주체(사용자 및 응용 프로그램)와 허용되는 작업을 정의합니다. 예를 들어 특정 데이터베이스에 `database viewer` 대한 보안 역할이 있는 보안 주체는 제한된 테이블을 제외하고 해당 데이터베이스의 모든 엔터티를 쿼리하고 볼 수 있습니다.

보안 역할은 보안 주체 또는 보안 그룹(다른 보안 주체 또는 기타 보안 그룹이 있을 수 있음)과 연결할 수 있습니다. 보안 주체가 보안 리소스에서 작업을 수행하려고 하면 시스템은 보안 주체가 리소스에서 이 작업을 수행할 수 있는 권한을 부여하는 하나 이상의 보안 역할과 연결되어 있는지 확인합니다. 이를 권한 **부여 검사라고**합니다. 권한 부여 검사에 실패하면 작업이 중단됩니다.

**구문**

보안 역할 관리 명령의 구문:

*동사* *ScurableObjectType* *보안 가능한 개체 이름* *역할* `(` [ *ListOfPrincipals* `)` [*설명]]*

* *동사는* `.show`수행할 `.add` `.drop` `.set`동작의 종류를 나타냅니다.

    |*동사* |Description                                  |
    |-------|---------------------------------------------|
    |`.show`|현재 값 또는 값을 반환합니다.         |
    |`.add` |역할에 하나 이상의 보안 주체를 추가합니다.     |
    |`.drop`|역할에서 하나 이상의 보안 주체를 제거합니다.|
    |`.set` |역할을 특정 보안 주체 목록으로 설정하여 이전의 모든 보안 주체(있는 경우)를 제거합니다.|

* *SecurableObjectType* 역할을 지정하는 개체의 종류입니다.

    |*증권 개체 유형*|Description|
    |---------------------|-----------|
    |`database`|지정된 데이터베이스|
    |`table`|지정된 테이블|

* *SecurableObjectName은* 개체의 이름입니다.

* *역할은* 관련 역할의 이름입니다.

    |*Role*      |Description|
    |------------|-----------|
    |`principals`|동사의 일부로만 나타날 `.show` 수 있습니다. 은 보안 가능한 개체에 영향을 줄 수 있는 보안 주체 목록을 반환합니다.|
    |`admins`    |개체 및 모든 하위 개체를 보고 수정하고 제거하는 기능을 포함하여 securable 개체를 제어할 수 있습니다.|
    |`users`     |분리 가능한 객체를 보고 그 아래에 새 객체를 만들 수 있습니다.|
    |`viewers`   |분리 가능한 개체를 볼 수 있습니다.|
    |`unrestrictedviewers`|데이터베이스 수준에서만 제한된 테이블("일반" `viewers` 및 `users`)에 노출되지 않는 테이블을 볼 수 있습니다.|
    |`ingestors` |데이터베이스 수준에서만 모든 테이블에 데이터 수집을 허용합니다.|
    |`monitors`  ||

* *ListOfPrincipals는* 선택적 쉼표 구분된 보안 보안 보안 식별자(형식의 `string`값)입니다.

* *설명은* 향후 감사를 `string` 위해 연결과 함께 저장된 형식의 선택적 값입니다.

## <a name="example"></a>예제

다음 컨트롤 명령에는 데이터베이스의 테이블에 `StormEvents` 대한 일부 액세스 권한이 있는 모든 보안 주체가 나열됩니다.

```kusto
.show table StormEvents principals
```

이 명령의 잠재적인 결과는 다음과 같습니다.

|역할 |PrincipalType |보안 주체 표시 이름 |보안 주체 개체 ID |교장FQN 
|---|---|---|---|---
|데이터베이스 Apsty 관리자 |AAD 사용자 |마크 스미스 |cd709aed-a26c-e3953dec735e |aaduser =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>데이터베이스 보안 역할 관리

`.set``database` *데이터베이스 이름* `skip-results` *역할* `none` [ ]

`.set``database` *데이터베이스 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

`.add``database` *데이터베이스 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

`.drop``database` *데이터베이스 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

첫 번째 명령은 역할에서 모든 보안 주체를 제거합니다. 두 번째 보안 주체는 역할에서 모든 보안 주체를 제거하고 새 보안 주체 집합을 설정합니다. 세 번째 보안 주체는 기존 보안 주체를 제거하지 않고 역할에 새 보안 주체를 추가합니다. 마지막은 역할에서 표시된 보안 주체를 제거하고 다른 보안 주체를 유지합니다.

위치:

* *데이터베이스Name은* 보안 역할이 수정되는 데이터베이스의 이름입니다.

* *역할은* 다음과 `admins` `ingestors`같은 `monitors` `unrestrictedviewers`가시입니다. `users` `viewers`

* *보안 주체는* 하나 이상의 보안 주체입니다. 이러한 보안 주체를 지정하는 방법은 [보안 주체 및 ID 공급자를](./access-control/principals-and-identity-providers.md) 참조하십시오.

* `skip-results`이 요청이 제공된 경우 명령이 업데이트된 데이터베이스 주체 목록을 반환하지 않도록 요청합니다.

* *설명이*제공된 경우 변경과 연결되고 해당 `.show` 명령으로 검색되는 텍스트입니다.

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>테이블 보안 역할 관리

`.set``table` *테이블 이름* `skip-results` *역할* `none` [ ]

`.set``table` *테이블 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

`.add``table` *테이블 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

`.drop``table` *테이블 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

첫 번째 명령은 역할에서 모든 보안 주체를 제거합니다. 두 번째 보안 주체는 역할에서 모든 보안 주체를 제거하고 새 보안 주체 집합을 설정합니다. 세 번째 보안 주체는 기존 보안 주체를 제거하지 않고 역할에 새 보안 주체를 추가합니다. 마지막은 역할에서 표시된 보안 주체를 제거하고 다른 보안 주체를 유지합니다.

위치:

* *TableName은* 보안 역할이 수정되는 테이블의 이름입니다.

* *역할은* 다음과 `admins` `ingestors`같은 것입니다.

* *보안 주체는* 하나 이상의 보안 주체입니다. 이러한 보안 주체를 지정하는 방법은 [보안 주체 및 ID 공급자를](./access-control/principals-and-identity-providers.md) 참조하십시오.

* `skip-results`이 요청이 제공된 경우 명령이 업데이트된 테이블 보안 주체 목록을 반환하지 않도록 요청합니다.

* *설명이*제공된 경우 변경과 연결되고 해당 `.show` 명령으로 검색되는 텍스트입니다.

**예제**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-function-security-roles"></a>기능 보안 역할 관리

`.set``function` *함수 이름* `skip-results` *역할* `none` [ ]

`.set``function` *함수 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

`.add``function` *함수 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

`.drop``function` *함수 이름* *Role* `(` 역할`,` 보안 *주체* [ 보안 *주체*...] `)` [`skip-results`[ [ [*[ 설명*]

첫 번째 명령은 역할에서 모든 보안 주체를 제거합니다. 두 번째 보안 주체는 역할에서 모든 보안 주체를 제거하고 새 보안 주체 집합을 설정합니다. 세 번째 보안 주체는 기존 보안 주체를 제거하지 않고 역할에 새 보안 주체를 추가합니다. 마지막은 역할에서 표시된 보안 주체를 제거하고 다른 보안 주체를 유지합니다.

위치:

* *FunctionName은* 보안 역할이 수정되는 함수의 이름입니다.

* *역할은* 항상 `admin`.

* *보안 주체는* 하나 이상의 보안 주체입니다. 이러한 보안 주체를 지정하는 방법은 [보안 주체 및 ID 공급자를](./access-control/principals-and-identity-providers.md) 참조하십시오.

* `skip-results`이 요청이 제공된 경우 명령이 업데이트된 함수 주체 목록을 반환하지 않도록 요청합니다.

* *설명이*제공된 경우 변경과 연결되고 해당 `.show` 명령으로 검색되는 텍스트입니다.

**예제**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

