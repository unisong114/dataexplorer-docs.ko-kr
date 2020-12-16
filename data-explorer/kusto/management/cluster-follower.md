---
title: 클러스터 종동체 명령-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 클러스터 종동체 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: efa7c3237ce85938634b1c8b31b1c3e10d01a6f8
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97514091"
---
# <a name="cluster-follower-commands"></a>클러스터 종동체 명령

종동체 클러스터 구성을 관리 하기 위한 제어 명령이 아래에 나열 되어 있습니다. 이러한 명령은 동기적으로 실행 되지만 다음 정기 스키마 새로 고침에 적용 됩니다. 따라서 새 구성이 적용 될 때까지 몇 분 정도 지연 될 수 있습니다.

종동체 명령에는 [데이터베이스 수준 명령과](#database-level-commands) [테이블 수준 명령이](#table-level-commands)있습니다.

## <a name="database-policy-overrides"></a>데이터베이스 정책 재정의

팔 로우 하는 데이터베이스에는 팔로 워 클러스터에서 재정의 된 [캐싱 정책](#caching-policy) 및 [권한 있는 사용자](#authorized-principals) 의 데이터베이스 수준 정책이 있을 수 있습니다.

### <a name="caching-policy"></a>캐싱 정책

종동체 클러스터에 대 한 기본 [캐싱 정책은](cachepolicy.md) 리더 클러스터 데이터베이스 및 테이블 수준 캐싱 정책을 유지 합니다.

|옵션             |Description                                                                                                                                                                                                           |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|**없음**(기본값) |사용 되는 캐싱 정책은 원본 데이터베이스 (리더 클러스터)에 정의 된 캐싱 정책입니다.                                                                                                                           |
|**replace**        |리더 클러스터 데이터베이스의 원본 데이터베이스 및 테이블 수준 캐싱 정책이 제거 됩니다 (로 설정 `null` ). 이러한 데이터베이스는 정의 된 경우 데이터베이스 및 테이블 수준 재정의 정책에 정의 된 것으로 대체 됩니다.|
|**union**          |리더 클러스터 데이터베이스의 원본 데이터베이스 및 테이블 수준 캐싱 정책은 데이터베이스 및 테이블 수준 재정의 정책에 정의 된 데이터베이스와 결합 됩니다.                                              |

> [!NOTE]
>  * 재정의 데이터베이스 및 테이블 수준 캐싱 정책의 컬렉션이 *비어* 있는 경우 *모든 것* 이 기본적으로 캐시 됩니다.
>  * 데이터베이스 수준 캐싱 정책 재정의를로 설정 하면 `0d` 기본적으로 캐시 되지 *않습니다* .

### <a name="authorized-principals"></a>권한 있는 보안 주체

권한이 부여 된 기본 [보안 주체](access-control/index.md#authorization) 는 리더 클러스터에 권한이 부여 된 보안 주체의 원본 데이터베이스를 유지 합니다.

|옵션             |Description                                                                                                                              |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
|**없음**(기본값) |사용 되는 인증 된 보안 주체는 원본 데이터베이스 (리더 클러스터)에 정의 되어 있습니다.                                         |
|**replace**        |리더 클러스터 인증 된 보안 주체의 원본 데이터베이스는 권한 부여 된 보안 주체에 정의 된 것으로 대체 됩니다.  |
|**union**          |리더 클러스터 인증 된 보안 주체의 원본 데이터베이스는 결합 권한 부여 된 보안 주체에 정의 된 것과 같습니다. |

> [!NOTE]
> 권한 부여 된 보안 주체의 컬렉션이 *비어* 있는 경우에는 데이터베이스 수준 보안 주체가 없습니다.

## <a name="table-policy-overrides"></a>테이블 정책 재정의

팔 로우 하는 데이터베이스의 테이블에는 종동체 클러스터에서 재정의 된 테이블 수준 [캐싱 정책이](cachepolicy.md) 있을 수 있습니다.
기본값은 원본 테이블의 캐싱 정책을 유지 하는 것입니다. 이 정책이 원본 데이터베이스에 있으면 종동체 클러스터에서 적용 됩니다.
`replace`옵션은 지원 됨-이 옵션을 사용 하는 경우 원본 테이블의 캐싱 정책이 재정의로 정의 된 것으로 대체 됩니다.

## <a name="database-level-commands"></a>데이터베이스 수준 명령

### <a name="show-follower-database"></a>. 종동체 데이터베이스 표시

하나 이상의 데이터베이스 수준 재정의가 구성 된 다른 리더 클러스터에서 데이터베이스 (또는 데이터베이스)를 표시 합니다.

**구문**

`.show``follower` `database` *DatabaseName*

`.show``follower` `databases` `(` *DatabaseName1* `,` ...`,` *DatabaseNameN*`)`

**출력** 

| 출력 매개 변수                     | 유형    | Description                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | 뒤에 오는 데이터베이스의 이름입니다.                                                                           |
| LeaderClusterMetadataPath            | String  | 리더 클러스터의 메타 데이터 컨테이너에 대 한 경로입니다.                                                               |
| CachingPolicyOverride                | String  | JSON 또는 null로 serialize 된 데이터베이스에 대 한 재정의 캐싱 정책입니다.                                         |
| AuthorizedPrincipalsOverride         | String  | JSON 또는 null로 serialize 된 데이터베이스에 대 한 권한이 있는 사용자의 재정의 컬렉션입니다.                    |
| AuthorizedPrincipalsModificationKind | String  | AuthorizedPrincipalsOverride (또는)를 사용 하 여 적용할 수정 종류 `none` `union` `replace` 입니다.                  |
| CachingPoliciesModificationKind      | String  | 데이터베이스 또는 테이블 수준 캐싱 정책 재정의 (또는)를 사용 하 여 적용할 수정 종류 `none` `union` `replace` 입니다. |
| IsAutoPrefetchEnabled                | 부울 | 각 스키마를 새로 고칠 때 새 데이터를 미리 페치할 지 여부를 지정 합니다.        |
| TableMetadataOverrides               | String  | 테이블 수준 속성을 재정의 하는 JSON serialization (정의 된 경우)                                      |

### <a name="alter-follower-database-policy-caching"></a>. alter 종동체 데이터베이스 정책 캐싱

는 리더 클러스터의 원본 데이터베이스에 설정 된 항목을 재정의 하도록 종동체 데이터베이스 캐싱 정책을 변경 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고**

* `modification kind`캐싱 정책에 대 한 기본값은 `union` 입니다. 을 변경 하려면 `modification kind` 명령을 사용 [`.alter follower database caching-policies-modification-kind`](#alter-follower-database-caching-policies-modification-kind) 합니다.
* 다음 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다 `.show` .
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 변경을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기를 사용 하 여 다음을 수행할 수 있습니다. [`.show follower database`](#show-follower-database)

**구문**

`.alter``follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *HotDataSpan*

**예제**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. 종동체 데이터베이스 정책 캐싱을 삭제 합니다.

종동체 데이터베이스 재정의 캐싱 정책을 삭제 합니다. 이렇게 하면 리더 클러스터의 원본 데이터베이스에 설정 된 정책이 적용 됩니다.
[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다. 

**참고**

* 다음 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다 `.show` .
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 다음을 사용 하 여 변경 작업을 수행할 수 있는 경우 종동체 데이터베이스의 재정의 설정 보기 [`.show follower database`](#show-follower-database)

**구문**

`.delete``follower` `database`  DatabaseName `policy``caching`

**예제**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. 종동체 데이터베이스 보안 주체 추가

승인 된 보안 주체의 종동체 데이터베이스 컬렉션에 인증 된 보안 주체를 추가 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고**

* `modification kind`이러한 권한이 부여 된 보안 주체에 대 한 기본값은 `none` 입니다. 을 변경 하려면 `modification kind`  [alter 종동체 데이터베이스 보안 주체-수정 유형](#alter-follower-database-principals-modification-kind)을 사용 합니다.
* 다음 명령을 사용 하 여 변경 작업을 수행할 수 있는 경우 유효한 보안 주체 컬렉션을 봅니다 `.show` .
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.add``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) Role `(`  `,` principal1 `,` ... *Principaln* `)` [ `'` *참고* `'` ]

**예제**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>. 삭제 팔로 워 데이터베이스 보안 주체

권한 부여 된 보안 주체의 종동체 데이터베이스 컬렉션에서 인증 된 보안 주체를 삭제 합니다.
[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고**

* 다음 명령을 사용 하 여 변경 작업을 수행할 수 있는 경우 유효한 보안 주체 컬렉션을 봅니다 `.show` .
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* 다음을 사용 하 여 변경 작업을 수행할 수 있는 경우 종동체 데이터베이스의 재정의 설정 보기 [`.show follower database`](#show-follower-database)

**구문**

`.drop``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) `(`  `,` principal1 `,` ... *Principaln*`)`

**예제**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. alter 종동체 데이터베이스 보안 주체-수정-종류

종동체 데이터베이스 권한 부여 된 보안 주체 수정 종류를 변경 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고**

* 다음 명령을 사용 하 여 변경 작업을 수행할 수 있는 경우 유효한 보안 주체 컬렉션을 봅니다 `.show` .
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.alter``follower` `database` *DatabaseName* 
 `principals-modification-kind` = ( `none`  |  `union`  |  `replace` )

**예제**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. 변경 종동체 데이터베이스 캐싱-정책-수정-종류

종동체 데이터베이스 및 테이블 캐싱 정책 수정 종류를 변경 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고**

* 변경 후 표준 명령을 사용 하 여 데이터베이스/테이블 수준 캐싱 정책의 효과적인 컬렉션을 볼 수 있습니다 `.show` .
    * [`.show tables details`](show-tables-command.md)
    * [`.show database details`](../management/show-databases.md)
* 다음을 사용 하 여 변경 작업을 수행할 수 있는 경우 종동체 데이터베이스의 재정의 설정 보기 [`.show follower database`](#show-follower-database)

**구문**

`.alter``follower` `database` *DatabaseName* `caching-policies-modification-kind` = ( `none`  |  `union`  |  `replace` )

**예제**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>. 변경 종동체 데이터베이스 프리페치-익스텐트

종동체 클러스터는이 데이터를 쿼리할 수 있도록 하기 전에 기본 저장소에서 노드 SSD (캐시)로 새 데이터를 가져올 때까지 기다릴 수 있습니다.

다음 명령은 각 스키마를 새로 고칠 때 새 익스텐트를 미리 페치하는 종동체 데이터베이스 구성을 변경 합니다. 이 명령에는 [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

> [!WARNING]
> * 이 설정은 종동체 데이터베이스의 데이터에 대 한 새로 고침을 저하 시킬 수 있습니다.
> * 기본 구성은 이며 기본값을 사용 하는 것이 `false` 좋습니다.
> * 설정을로 변경 하도록 선택 하는 경우 `true` 구성이 변경 된 후 일정 기간 동안 유효성 검사에 미치는 영향을 면밀 하 게 평가 합니다.

**구문**

`.alter``follower` `database` *DatabaseName* `prefetch-extents` = ( `true`  |  `false` )

**예제**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>테이블 수준 명령

### <a name="alter-follower-table-policy-caching"></a>. alter 종동체 테이블 정책 캐싱

는 종동체 데이터베이스에서 테이블 수준 캐싱 정책을 변경 하 여 리더 클러스터의 원본 데이터베이스에 설정 된 정책을 재정의 합니다.
[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다. 

**참고**

* 다음 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다 `.show` .
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 다음을 사용 하 여 변경 작업을 수행할 수 있는 경우 종동체 데이터베이스의 재정의 설정 보기 [`.show follower database`](#show-follower-database)

**구문**

`.alter``follower` `database` *DatabaseName* 테이블 *TableName* `policy` `caching` `hot` `=` *HotDataSpan*

`.alter``follower` `database` *DatabaseName* tables `(`  `,` TableName1 `,` ... *TableNameN* `)` `policy` `caching` `hot` `=` *HotDataSpan*

**예제**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. 종동체 테이블 정책 캐싱을 삭제 합니다.

종동체 데이터베이스에서 재정의 테이블 수준 캐싱 정책을 삭제 하 여 리더 클러스터의 원본 데이터베이스에 설정 된 정책이 적용 되도록 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다. 

**참고**

* 다음 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다 `.show` .
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 다음을 사용 하 여 변경 작업을 수행할 수 있는 경우 종동체 데이터베이스의 재정의 설정 보기 [`.show follower database`](#show-follower-database)

**구문**

`.delete``follower` `database` *DatabaseName* `table`  TableName `policy``caching`

`.delete``follower` `database` *DatabaseName* `tables` `(`  `,` TableName1 `,` ... *TableNameN* `)` `policy``caching`

**예제**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>샘플 구성

다음은 종동체 데이터베이스를 구성 하는 샘플 단계입니다.

이 예제에 대한 설명:

* Microsoft의 팔로 워 클러스터는 `MyFollowerCluster` `MyDatabase` 리더 클러스터에서 다음 데이터베이스 `MyLeaderCluster` 입니다.
    * `MyDatabase` 에는 `N` `MyTable1` ,, `MyTable2` `MyTable3` , ... `MyTableN` ( `N` > 3) 등의 테이블이 있습니다.
    * `MyLeaderCluster`에서:
    
    | `MyTable1` 캐싱 정책 | `MyTable2` 캐싱 정책 | `MyTable3`...`MyTableN` 캐싱 정책   | `MyDatabase` 권한 있는 보안 주체                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 핫 데이터 범위 = `7d`      | 핫 데이터 범위 = `30d`     | 핫 데이터 범위 = `365d`                   | *뷰어*  =  `aadgroup=scubadivers@contoso.com` ; *관리자* = `aaduser=jack@contoso.com` |
     
    * `MyFollowerCluster`원하는 작업:
    
    | `MyTable1` 캐싱 정책 | `MyTable2` 캐싱 정책 | `MyTable3`...`MyTableN` 캐싱 정책   | `MyDatabase` 권한 있는 보안 주체                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 핫 데이터 범위 = `1d`      | 핫 데이터 범위 = `3d`      | 핫 데이터 범위 = `0d` (아무 것도 캐시 되지 않음) | *관리자*  =  `aaduser=jack@contoso.com` , *뷰어* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster`와 `MyLeaderCluster` 는 모두 동일한 영역에 있어야 합니다.

### <a name="steps-to-execute"></a>실행 단계

*필수 구성 요소:* 클러스터 `MyFollowerCluster` 의 데이터베이스를 따르도록 클러스터를 설정 `MyDatabase` `MyLeaderCluster` 합니다.

> [!NOTE]
> 제어 명령을 실행 하는 보안 주체는 `DatabaseAdmin` 데이터베이스에 있어야 합니다 `MyDatabase` .

#### <a name="show-the-current-configuration"></a>현재 구성 표시

다음에 나오는에 따라 현재 구성을 확인 합니다 `MyDatabase` `MyFollowerCluster` .

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 열                              | 값                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|CachingPolicyOverride                | null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | None                                                     |
|IsAutoPrefetchEnabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>권한 있는 보안 주체 재정의

에 대 한 권한이 있는 사용자의 컬렉션을 `MyDatabase` `MyFollowerCluster` 하나의 aad 사용자를 데이터베이스 관리자로 포함 하 고 하나의 aad 사용자를 데이터베이스 뷰어로 포함 하는 컬렉션으로 바꿉니다.

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

이러한 두 개의 특정 보안 주체에만 액세스할 수 있는 권한이 부여 됩니다. `MyDatabase``MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| 역할                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | 메모 |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| 데이터베이스 MyDatabase 관리자  | AAD 사용자      | 잭 Kusto (upn: jack@contoso.com )       | 12345678-abcd-efef-1234-350bf486087b | aaduser = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| 데이터베이스 MyDatabase 뷰어 | AAD 사용자      | Jill Kusto (upn: jack@contoso.com )       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| aaduser = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |
| aaduser = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>캐싱 정책 재정의

각 테이블에 데이터를 캐시 하지 않도록 설정 하 여에 대 한 데이터베이스 및 테이블 수준 캐싱 정책의 컬렉션을 대체 합니다 `MyDatabase` `MyFollowerCluster` . 즉, 두 개의 특정 테이블을 제외 하  `MyTable1` `MyTable2` `1d` 고 각각 및의 기간에 대해 데이터를 캐시 합니다 `3d` .

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

이러한 두 개의 특정 테이블에만 데이터가 캐시 되 고 나머지 테이블에는 다음의 핫 데이터 기간이 있습니다 `0d` .

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| CachingPolicy                                                                | TableNames                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"DataHotSpan": {"Value": "1.00:00:00"}, "IndexHotSpan": {"Value": "1.00:00:00"}} | ["MyTable1"]                |
| {"DataHotSpan": {"Value": "3.00:00:00"}, "IndexHotSpan": {"Value": "3.00:00:00"}} | ["MyTable2"]                |
| {"DataHotSpan": {"Value": "0.00:00:00"}, "IndexHotSpan": {"Value": "0.00:00:00"}} | ["MyTable3",..., "MyTableN"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| TableMetadataOverrides                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1": {"CachingPolicyOverride": {"DataHotSpan": {"Value": "1.00:00:00"}, "IndexHotSpan": {"Value": "1.00:00:00"}}}} |
| {"MyTable2": {"CachingPolicyOverride": {"DataHotSpan": {"Value": "3.00:00:00"}, "IndexHotSpan": {"Value": "3.00:00:00"}}}} |

#### <a name="summary"></a>요약

`MyDatabase`가 뒤에 나오는 현재 구성을 참조 하십시오 `MyFollowerCluster` .

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 열                              | 값                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"DataHotSpan": {"Value": "00:00:00"}, "IndexHotSpan": {"Value": "00:00:00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal": {"FullyQualifiedName": "aaduser = 87654321-abcd-efef-1234-350bf486087b",...}, {"Principal": {"FullyQualifiedName": "aaduser = 54321789-abcd-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | 바꾸기                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable": {"CachingPolicyOverride": {"DataHotSpan": {"Value": "3.00:00:00"} ...}, "MySourceTable": {"CachingPolicyOverride": {"DataHotSpan": {"Value": "1.00:00:00"},...}}}       |
|CachingPoliciesModificationKind      | 바꾸기                                                                                                                                                                         |
