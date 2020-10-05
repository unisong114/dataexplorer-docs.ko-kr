---
title: 쿼리 제한 정책 명령-Azure 데이터 탐색기
description: 이 문서에서는 Azure의 쿼리 제한 정책 명령에 대해 설명 데이터 탐색기
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 85408aadfd15fc48f1e3e86ab01afce5dca15bce
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734017"
---
# <a name="query-throttling-policy-commands"></a>쿼리 제한 정책 명령

[쿼리 제한 정책은](query-throttling-policy.md) 클러스터에서 쿼리 동시성을 제한 하는 클러스터 수준 정책입니다. 이러한 쿼리 제한 정책 명령에는 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요 합니다.

## <a name="query-throttling-policy-object"></a>쿼리 제한 정책 개체

클러스터에는 0 또는 1 개의 쿼리 조정 정책이 정의 되어 있을 수 있습니다.

클러스터에 쿼리 조정 정책이 정의 되어 있지 않은 경우 기본 정책이 적용 됩니다. 기본 정책에 대 한 자세한 내용은 [쿼리 제한](../concepts/querylimits.md)을 참조 하세요.

|속성  |Type    |설명                                                       |
|----------|--------|------------------------------------------------------------------|
|IsEnabled |`bool`  |쿼리 제한 정책을 사용 (true) 하거나 사용 하지 않도록 (false) 여부를 지정 합니다.     |
|MaxQuantity|`int`|클러스터에서 실행할 수 있는 동시 쿼리 수입니다. 숫자에는 양수 값이 있어야 합니다. |

## `.show cluster policy querythrottling`

클러스터의 [쿼리 제한 정책을](query-throttling-policy.md) 반환 합니다.

### <a name="syntax"></a>구문

`.show` `cluster` `policy` `querythrottling`

### <a name="returns"></a>반환

다음 열이 포함 된 테이블을 반환 합니다.

|Column    |형식    |설명
|---|---|---
|PolicyName| 문자열 |정책 이름-QueryThrottlingPolicy
|EntityName| 문자열 |Empty
|정책    | 문자열 |쿼리 조정 정책 [개체로](#query-throttling-policy-object) 형식이 지정 된 쿼리 조정 정책을 정의 하는 JSON 개체입니다.

### <a name="example"></a>예제

<!-- csl -->
```
.show cluster policy querythrottling 
```

HRESULT = NO_ERROR를

|PolicyName|EntityName|정책|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled": true, "MaxQuantity": 25}

## `.alter cluster policy querythrottling`

지정 된 테이블의 [쿼리 조정 정책을](query-throttling-policy.md) 설정 합니다. 

### <a name="syntax"></a>구문

`.alter``cluster` `policy` `querythrottling` *QueryThrottlingPolicyObject*

### <a name="arguments"></a>인수

*QueryThrottlingPolicyObject* 는 쿼리 조정 정책 개체가 정의 된 JSON 개체입니다.

### <a name="returns"></a>반환

클러스터 쿼리 제한 정책 개체를 설정 하 고, 정의 된 현재 정책을 재정의 하 고, 해당 하는 [. show cluster policy `querythrottling` ](#show-cluster-policy-querythrottling) 명령의 출력을 반환 합니다.

### <a name="example"></a>예제

<!-- csl -->
```
.alter cluster policy querythrottling '{"IsEnabled": true, "MaxQuantity": 25}'
```

|PolicyName|EntityName|정책|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled": true, "MaxQuantity": 25}

## `.delete cluster policy querythrottling`

클러스터 [쿼리 제한 정책](query-throttling-policy.md) 개체를 삭제 합니다.

### <a name="syntax"></a>구문

`.delete` `cluster` `policy` `querythrottling`
