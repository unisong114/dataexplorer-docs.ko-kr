---
title: 연산자 확장 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 확장 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b9b7bdb9488b0e9d1b72b3e0ab4782020c9b841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515592"
---
# <a name="extend-operator"></a>extend 연산자

계산된 열을 만들고 결과 집합에 부가합니다.

```kusto
T | extend duration = endTime - startTime
```

**구문**

*T* `| extend` [*열* | `(`이름`,` 열*이름*[...] ] *표현식* [...]`,` `)` `=`

**인수**

* *T*: 입력 테이블 형식 결과 집합입니다.
* *열 이름:* 선택적. 추가하거나 업데이트할 열의 이름입니다. 생략하면 이름이 생성됩니다. *Expression이* 두 개 이상의 열을 반환하는 경우 괄호 안에 열 이름 목록을 지정할 수 있습니다. 이 경우 *Expression의*출력 열에는 지정된 이름이 지정되어 나머지 출력 열이 있는 경우 출력 열이 삭제됩니다. 열 이름 목록을 지정하지 않으면 생성된 이름이 있는 모든 *Expression의*출력 열이 출력에 추가됩니다.
* *표현식:* 입력의 열을 둘러싼 계산입니다.

**반환**

다음과 같은 입력 테이블 형식 결과 집합의 복사본입니다.
1. 입력에 `extend` 이미 있는 것으로 표시된 열 이름은 제거되고 새 계산값으로 추가됩니다.
2. 입력에 `extend` 존재하지 않는 열 이름은 새 계산값으로 추가됩니다.

**팁**

* 연산자는 `extend` 인덱스가 **없는** 입력 결과 집합에 새 열을 추가합니다. 대부분의 경우 새 열이 인덱스가 있는 기존 테이블 열과 정확히 동일하게 설정된 경우 Kusto는 기존 인덱스를 자동으로 사용할 수 있습니다. 그러나 일부 복잡한 시나리오에서는 이 전파가 수행되지 않습니다. 이러한 경우 컬럼의 이름을 바꾸려는 경우 [ `project-rename` 연산자](projectrenameoperator.md) 대신 사용하십시오.

**예제**

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

[series_stats](series-statsfunction.md) 함수를 사용하여 여러 열을 반환할 수 있습니다.