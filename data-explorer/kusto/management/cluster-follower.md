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
ms.openlocfilehash: 73b65cc59ff98bc658c542d690812ccf5ad3895a
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108459"
---
# <a name="cluster-follower-commands"></a>클러스터 종동체 명령

종동체 클러스터 구성을 관리 하기 위한 제어 명령이 아래에 나열 되어 있습니다. 이러한 명령은 동기적으로 실행 되지만 다음 정기 스키마 새로 고침에 적용 됩니다. 따라서 새 구성이 적용 될 때까지 몇 분 정도 지연 될 수 있습니다.

종동체 명령에는 [데이터베이스 수준 명령과](#database-level-commands) [테이블 수준 명령이](#table-level-commands)있습니다.

## <a name="database-level-commands"></a>데이터베이스 수준 명령

### <a name="show-follower-database"></a>. 종동체 데이터베이스 표시

하나 이상의 데이터베이스 수준 재정의가 구성 된 다른 리더 클러스터에서 데이터베이스 (또는 데이터베이스)를 표시 합니다.


**구문**

`.show``follower` *DatabaseName* DatabaseName `database`

`.show``follower` *DatabaseName1*DatabaseName1 ... `databases` `(``,` `,` *DatabaseNameN*`)`

**출력** 

| 출력 매개 변수                     | Type    | Description                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | 뒤에 오는 데이터베이스의 이름입니다.                                                                           |
| LeaderClusterMetadataPath            | String  | 리더 클러스터의 메타 데이터 컨테이너에 대 한 경로입니다.                                                               |
| CachingPolicyOverride                | String  | JSON 또는 null로 serialize 된 데이터베이스에 대 한 재정의 캐싱 정책입니다.                                         |
| AuthorizedPrincipalsOverride         | String  | JSON 또는 null로 serialize 된 데이터베이스에 대 한 권한이 있는 사용자의 재정의 컬렉션입니다.                    |
| AuthorizedPrincipalsModificationKind | String  | AuthorizedPrincipalsOverride (`none` `union` 또는 `replace`)를 사용 하 여 적용할 수정 종류입니다.                  |
| CachingPoliciesModificationKind      | String  | 데이터베이스 또는 테이블 수준 캐싱 정책 재정의 (`none` `union` 또는 `replace`)를 사용 하 여 적용할 수정 종류입니다. |
| IsAutoPrefetchEnabled                | 부울 | 각 스키마를 새로 고칠 때 새 데이터를 미리 페치할 지 여부를 지정 합니다.        |
| TableMetadataOverrides               | String  | 테이블 수준 속성을 재정의 하는 JSON serialization (정의 된 경우)                                      |

### <a name="alter-follower-database-policy-caching"></a>. alter 종동체 데이터베이스 정책 캐싱

는 리더 클러스터의 원본 데이터베이스에 설정 된 항목을 재정의 하도록 종동체 데이터베이스 캐싱 정책을 변경 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.



**참고 사항**

* 캐싱 정책 `modification kind` 에 대 한 기본값 `union`은입니다. 을 변경 하려면 `modification kind` [. alter 종동체 데이터베이스 캐싱-정책-수정-kind](#alter-follower-database-caching-policies-modification-kind) 명령을 사용 합니다.
* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다.
    * [. 데이터베이스 정책 보존 표시](../management/retention-policy.md#show-retention-policy)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* 을 (를) 사용 하 여 변경 작업을 수행한 후에는 종동체 데이터베이스의 재정의 설정을 볼 수 있습니다 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.alter``follower` `=` *HotDataSpan* *DatabaseName* DatabaseName HotDataSpan `policy` `caching` `database` `hot`



**예제**



```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. 종동체 데이터베이스 정책 캐싱을 삭제 합니다.

종동체 데이터베이스 재정의 캐싱 정책을 삭제 합니다. 이렇게 하면 리더 클러스터의 원본 데이터베이스에 설정 된 정책이 적용 됩니다.
[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다. 

**참고 사항**

* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다.
    * [. 데이터베이스 정책 보존 표시](../management/retention-policy.md#show-retention-policy)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.delete``follower` `policy` *DatabaseName* DatabaseName `database``caching`

**예제**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. 종동체 데이터베이스 보안 주체 추가

승인 된 보안 주체의 종동체 데이터베이스 컬렉션에 인증 된 보안 주체를 추가 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.



**참고 사항**

* 이러한 권한이 `modification kind` 부여 된 보안 주체에 `none`대 한 기본값은입니다. 을 변경 하려면 `modification kind` [alter 종동체 데이터베이스 보안 주체-수정 유형](#alter-follower-database-principals-modification-kind)을 사용 합니다.
* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행할 수 있는 경우 유효한 보안 주체 컬렉션을 봅니다.
    * [. 데이터베이스 보안 주체 표시](../management/security-roles.md#managing-database-security-roles)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.add``follower` `users` *DatabaseName* `(` *principal1*DatabaseName (`admins``,`) Role | principal1 ...`viewers` |  `database`  | `monitors` `,` *principaln* `)` [`'`*notes*참고`'`]




**예제**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

```kusto

```

### <a name="drop-follower-database-principals"></a>. 삭제 팔로 워 데이터베이스 보안 주체

권한 부여 된 보안 주체의 종동체 데이터베이스 컬렉션에서 인증 된 보안 주체를 삭제 합니다.
[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고 사항**

* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행할 수 있는 경우 유효한 보안 주체 컬렉션을 봅니다.
    * [. 데이터베이스 보안 주체 표시](../management/security-roles.md#managing-database-security-roles)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.drop``follower` `monitors` *DatabaseName* `(` *principal1*DatabaseName (`admins` | `,`) principal1 | ...`users` |  `database` `viewers` `,` *principaln*`)`

**예제**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. alter 종동체 데이터베이스 보안 주체-수정-종류

종동체 데이터베이스 권한 부여 된 보안 주체 수정 종류를 변경 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고 사항**

* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행할 수 있는 경우 유효한 보안 주체 컬렉션을 봅니다.
    * [. 데이터베이스 보안 주체 표시](../management/security-roles.md#managing-database-security-roles)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.alter``follower`  |  *DatabaseName*  | DatabaseName`replace`= (`none`) `database` 
 `principals-modification-kind` `union`



**예제**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. 변경 종동체 데이터베이스 캐싱-정책-수정-종류

종동체 데이터베이스 및 테이블 캐싱 정책 수정 종류를 변경 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**참고 사항**

* 변경 후 표준 `.show` 명령을 사용 하 여 데이터베이스/테이블 수준 캐싱 정책의 효과적인 컬렉션을 볼 수 있습니다.
    * [. 테이블 세부 정보 표시](show-tables-command.md)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.alter``follower` `union` *DatabaseName* `replace`DatabaseName = ()`none` |  `database` `caching-policies-modification-kind`  | 



**예제**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```



## <a name="table-level-commands"></a>테이블 수준 명령

### <a name="alter-follower-table-policy-caching"></a>. alter 종동체 테이블 정책 캐싱

는 종동체 데이터베이스에서 테이블 수준 캐싱 정책을 변경 하 여 리더 클러스터의 원본 데이터베이스에 설정 된 정책을 재정의 합니다.
[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다. 



**참고 사항**

* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다.
    * [. 데이터베이스 정책 보존 표시](../management/retention-policy.md#show-retention-policy)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**





`.alter``follower` `=` *HotDataSpan* *DatabaseName* `caching` *TableName* DatabaseName 테이블 TableName `policy` HotDataSpan `database` `hot`

`.alter``follower` `(` *DatabaseName* `,`DatabaseName tables *TableName1*... `database` `,` *TableNameN* TableNameN`)` *HotDataSpan* HotDataSpan `policy` `caching` `hot` `=`

**예제**



```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. 종동체 테이블 정책 캐싱을 삭제 합니다.

종동체 데이터베이스에서 재정의 테이블 수준 캐싱 정책을 삭제 하 여 리더 클러스터의 원본 데이터베이스에 설정 된 정책이 적용 되도록 합니다. [Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다. 

**참고 사항**

* 다음 `.show` 명령을 사용 하 여 변경 작업을 수행한 후 정책 또는 유효 정책을 볼 수 있습니다.
    * [. 데이터베이스 정책 보존 표시](../management/retention-policy.md#show-retention-policy)
    * [. 데이터베이스 세부 정보 표시](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* 를 사용 하 여 변경 작업을 수행한 후에 종동체 데이터베이스의 재정의 설정 보기 [. 종동체 데이터베이스 표시](#show-follower-database)

**구문**

`.delete``follower` `table` *TableName* *DatabaseName* DatabaseName TableName `database` `policy``caching`

`.delete``follower` `(` *TableName1* *DatabaseName* DatabaseName `tables` ... `database` `,` `,``)` *TableNameN* TableNameN `policy``caching`

**예제**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>샘플 구성

다음은 종동체 데이터베이스를 구성 하는 샘플 단계입니다.

이 예제에 대한 설명:

* Microsoft의 팔로 `MyFollowerCluster` 워 클러스터는 리더 `MyDatabase` 클러스터에서 다음 데이터베이스 `MyLeaderCluster`입니다.
    * `MyDatabase``N` 테이블 `MyTable1`:, `MyTable2`, `MyTable3`, ... `MyTableN` (`N` > 3).
    * `MyLeaderCluster`에서:
    
    | `MyTable1`캐싱 정책 | `MyTable2`캐싱 정책 | `MyTable3`... `MyTableN` 캐싱 정책   | `MyDatabase`권한 있는 보안 주체                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 핫 데이터 범위 =`7d`      | 핫 데이터 범위 =`30d`     | 핫 데이터 범위 =`365d`                   | *Viewers* = 뷰어`aadgroup=scubadivers@contoso.com`; *관리자* = `aaduser=jack@contoso.com` |
     
    * `MyFollowerCluster` 원하는 작업:
    
    | `MyTable1`캐싱 정책 | `MyTable2`캐싱 정책 | `MyTable3`... `MyTableN` 캐싱 정책   | `MyDatabase`권한 있는 보안 주체                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 핫 데이터 범위 =`1d`      | 핫 데이터 범위 =`3d`      | 핫 데이터 범위 = `0d` (아무 것도 캐시 되지 않음) | *Admins* = 관리자`aaduser=jack@contoso.com`, *뷰어* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> 와 `MyFollowerCluster` `MyLeaderCluster` 는 모두 동일한 영역에 있어야 합니다.

### <a name="steps-to-execute"></a>실행 단계

*필수 구성 요소:* 클러스터의 `MyFollowerCluster` 데이터베이스 `MyDatabase` 를 따르도록 클러스터를 `MyLeaderCluster`설정 합니다.

> [!NOTE]
> 제어 명령을 실행 하는 보안 주체는 데이터베이스 `DatabaseAdmin` `MyDatabase`에 있어야 합니다.

#### <a name="show-the-current-configuration"></a>현재 구성 표시

다음에 나오는에 따라 `MyDatabase` 현재 구성을 확인 합니다. `MyFollowerCluster`

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 열                              | Value                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|CachingPolicyOverride                | Null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | 없음                                                     |
|IsAutoPrefetchEnabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>권한 있는 보안 주체 재정의

에 대 한 `MyDatabase` 권한이 있는 사용자의 컬렉션 `MyFollowerCluster` 을 하나의 aad 사용자를 데이터베이스 관리자로 포함 하 고 하나의 aad 사용자를 데이터베이스 뷰어로 포함 하는 컬렉션으로 바꿉니다.

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

이러한 두 개의 특정 보안 주체에만 액세스할 `MyDatabase` 수 있는 권한이 부여 됩니다.`MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| 역할                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | 메모 |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| 데이터베이스 MyDatabase 관리자  | AAD 사용자      | 잭 Kusto (upn: jack@contoso.com)       | 12345678-abcd-efef-1234-350bf486087b | aaduser = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| 데이터베이스 MyDatabase 뷰어 | AAD 사용자      | Jill Kusto (upn: jack@contoso.com)       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

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

각 테이블에 데이터를 캐시 *하지 않도록* 설정 `MyTable1`하 여에 `MyDatabase` `MyFollowerCluster` 대 한 데이터베이스 및 테이블 수준 캐싱 정책의 컬렉션을 대체 합니다. 즉, 두 개의 특정 `MyTable2` 테이블을 제외 하 고 각각 및 `1d` `3d`의 기간에 대해 데이터를 캐시 합니다.

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

이러한 두 개의 특정 테이블에만 데이터가 캐시 되 고 나머지 테이블에는 다음의 `0d`핫 데이터 기간이 있습니다.

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

`MyDatabase` 가 뒤에 `MyFollowerCluster`나오는 현재 구성을 참조 하십시오.

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 열                              | Value                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"DataHotSpan": {"Value": "00:00:00"}, "IndexHotSpan": {"Value": "00:00:00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal": {"FullyQualifiedName": "aaduser = 87654321-abcd-efef-1234-350bf486087b",...}, {"Principal": {"FullyQualifiedName": "aaduser = 54321789-abcd-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | Replace                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable": {"CachingPolicyOverride": {"DataHotSpan": {"Value": "3.00:00:00"} ...}, "MySourceTable": {"CachingPolicyOverride": {"DataHotSpan": {"Value": "1.00:00:00"},...}}}       |
|CachingPoliciesModificationKind      | Replace                                                                                                                                                                         |