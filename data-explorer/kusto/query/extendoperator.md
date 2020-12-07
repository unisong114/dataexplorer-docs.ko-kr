---
title: extend 연산자 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 extend 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7ead6313128b99357dd61f18f55c5d5543943a05
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513355"
---
# <a name="extend-operator"></a>extend 연산자

계산 열을 만들고 결과 집합에 추가합니다.

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>Syntax

*T* `| extend` [*ColumnName* | `(`*ColumnName*[`,` ...]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 결과 집합입니다.
* *ColumnName:* (선택 사항) 추가하거나 업데이트할 열의 이름입니다. 생략하면 이름이 생성됩니다. *Expression* 이 둘 이상의 열을 반환하는 경우 열 이름 목록을 괄호로 지정할 수 있습니다. 이 경우 *Expression* 의 출력 열에 지정된 이름이 지정되고 나머지 출력 열이 있으면 삭제됩니다. 열 이름 목록을 지정하지 않으면 생성된 이름이 있는 모든 *Expression* 의 출력 열이 출력에 추가됩니다.
* *식:* 입력 열에 대한 계산.

## <a name="returns"></a>반환

다음과 같은 입력 테이블 형식 결과 집합의 복사본입니다.
1. 입력에 이미 있는 `extend`로 표시된 열 이름은 제거되고 새로 계산된 값으로 추가됩니다.
2. 입력에 없는 `extend`로 표시된 열 이름은 새로 계산된 값으로 추가됩니다.

**팁**

* `extend` 연산자는 인덱스가 **없는** 입력 결과 집합에 새 열을 추가합니다. 대부분의 경우 새 열이 인덱스가 있는 기존 테이블 열과 정확히 동일하게 설정되면 Kusto는 기존 인덱스를 자동으로 사용할 수 있습니다. 그러나 일부 복잡한 시나리오에서는 이러한 전파가 수행되지 않습니다. 이러한 경우 열 이름을 바꾸는 것이 목표이면 [`project-rename` 연산자](projectrenameoperator.md)를 대신 사용합니다.

## <a name="example"></a>예제

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

[series_stats](series-statsfunction.md) 함수를 사용하여 여러 열을 반환할 수 있습니다.