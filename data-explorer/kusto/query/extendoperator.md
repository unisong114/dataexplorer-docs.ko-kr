---
title: operator 확장-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 확장 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0398efc3f97e9af1f994b16b91a9888fb4fcfa0b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243324"
---
# <a name="extend-operator"></a>extend 연산자

계산 열을 만들어 결과 집합에 추가 합니다.

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>구문

*T* `| extend` [*columnname*  |  `(` *columnname*[ `,` ...] `)` `=` ] *식* [ `,` ...]

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 결과 집합입니다.
* *ColumnName:* 필드. 추가 하거나 업데이트할 열의 이름입니다. 생략 하면 이름이 생성 됩니다. *식이* 둘 이상의 열을 반환 하는 경우 열 이름 목록을 괄호로 지정할 수 있습니다. 이 경우 *식*의 출력 열에는 지정 된 이름이 지정 되며 나머지 출력 열은 삭제 됩니다 (있는 경우). 열 이름 목록을 지정 하지 않으면 생성 된 이름이 있는 모든 *식*의 출력 열이 출력에 추가 됩니다.
* *식:* 입력 열에 대 한 계산입니다.

## <a name="returns"></a>반환

다음과 같은 입력 테이블 형식 결과 집합의 복사본입니다.
1. `extend`입력에 이미 있는 열 이름은 제거 되며 새 계산 된 값으로 추가 됩니다.
2. 입력에 존재 하지 않는에서 기록한 열 이름은 `extend` 새 계산 된 값으로 추가 됩니다.

**팁**

* `extend`연산자가 인덱스를 포함 **하지** 않는 입력 결과 집합에 새 열을 추가 합니다. 대부분의 경우 새 열이 인덱스를 포함 하는 기존 테이블 열과 정확히 동일 하 게 설정 된 경우 Kusto는 기존 인덱스를 자동으로 사용할 수 있습니다. 그러나 일부 복잡 한 시나리오에서는 이러한 전파가 수행 되지 않습니다. 이러한 경우 열 이름을 바꾸는 것이 목표 인 경우에는 [ `project-rename` 연산자](projectrenameoperator.md) 를 대신 사용 합니다.

## <a name="example"></a>예제

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

[Series_stats](series-statsfunction.md) 함수를 사용 하 여 여러 열을 반환할 수 있습니다.