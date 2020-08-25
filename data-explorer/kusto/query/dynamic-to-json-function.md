---
title: dynamic_to_json ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 dynamic_to_json ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 94901a69b4c4435e983f39f1692cf45cf27756e5
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793786"
---
# <a name="dynamic_to_json"></a>dynamic_to_json()

`dynamic`입력을 문자열 표현으로 변환 합니다.
입력이 속성 모음 인 경우 출력 문자열은 키를 기준으로 정렬 된 콘텐츠를 재귀적으로 출력 합니다. 그렇지 않으면 출력은 함수 출력과 유사 합니다 `tostring` .

## <a name="syntax"></a>구문

`dynamic_to_json(Expr)`

## <a name="arguments"></a>인수

* *Expr*: `dynamic` input입니다. 함수는 하나의 인수를 허용 합니다.

## <a name="returns"></a>반환

입력의 문자열 표현을 반환 합니다 `dynamic` . 입력이 속성 모음 인 경우 출력 문자열은 키를 기준으로 정렬 된 콘텐츠를 재귀적으로 출력 합니다.

## <a name="examples"></a>예제

식:

```kusto
  let bag1 = dynamic_to_json(dynamic({ 'Y10':dynamic({ }), 'X8': dynamic({ 'c3':1, 'd8':5, 'a4':6 }),'D1':114, 'A1':12, 'B1':2, 'C1':3, 'A14':[15, 13, 18]}));
  print bag1
```
  
결과:

```
"{
  ""A1"": 12,
  ""A14"": [
    15,
    13,
    18
  ],
  ""B1"": 2,
  ""C1"": 3,
  ""D1"": 114,
  ""X8"": {
    ""c3"": 1,
    ""d8"": 5,
    ""a4"": 6
  },
  ""Y10"": {}
}"
```

식:

```kusto
 let bag2 = dynamic_to_json(dynamic({ 'X8': dynamic({ 'a4':6, 'c3':1, 'd8':5}), 'A14':[15, 13, 18], 'C1':3, 'B1':2, 'Y10': dynamic({ }), 'A1':12, 'D1':114}));
 print bag2
```
 
결과:

```
{
  "A1": 12,
  "A14": [
    15,
    13,
    18
  ],
  "B1": 2,
  "C1": 3,
  "D1": 114,
  "X8": {
    "a4": 6,
    "c3": 1,
    "d8": 5
  },
  "Y10": {}
}
```
