---
title: 샘플 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 샘플 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 89e9eea4e8a6a5922e9141818fc5832156ac8e72
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803559"
---
# <a name="sample-operator"></a>sample 연산자

입력 테이블에서 지정 된 수의 임의 행까지 반환 합니다.

```kusto
T | sample 5
```

> [!NOTE]
> * `sample`는 값을 배포 하는 대신 속도를 위해 설계 되었습니다. 특히, or 연산자와 같이 서로 다른 크기의 데이터 집합 2 개를 통합 하는 연산자를 사용 하는 경우 ' 공평 ' 결과를 생성 하지 않습니다 `union` `join` . 테이블 참조 및 필터 바로 뒤에를 사용 하는 것이 좋습니다 `sample` .
> * `sample`는 명확 하지 않은 연산자 이며 쿼리 중에 계산 될 때마다 다른 결과 집합을 반환 합니다. 예를 들어 다음 쿼리는 동일한 행을 두 번 반환 하는 경우에도 두 개의 다른 행을 생성 합니다.

## <a name="syntax"></a>구문

*T* `| sample` *numberofrows*

## <a name="arguments"></a>인수

* *Numberofrows*: 반환할 *T* 의 행 수입니다. 임의의 숫자 식을 지정할 수 있습니다.

## <a name="examples"></a>예

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

위의 예제에서 `_sample` 한 번만 계산 되도록 하려면 [구체화 ()](./materializefunction.md) 함수를 사용할 수 있습니다.

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

지정 된 수의 행이 아닌 특정 비율의 데이터를 샘플링 하려면 다음을 사용할 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

행이 아닌 키를 샘플링 하는 경우 (예: 샘플 10 Id 및 이러한 Id의 모든 행 가져오기)를 연산자와 함께 사용할 수 있습니다 [`sample-distinct`](./sampledistinctoperator.md) `in` .


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
