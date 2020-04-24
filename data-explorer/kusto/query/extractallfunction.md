---
title: extract_all() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 extract_all()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7623a064e272f8b96ca25cc6af47318e26dfb93
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515422"
---
# <a name="extract_all"></a>extract_all()

텍스트 문자열에서 [정규식에](./re2.md) 대한 모든 일치 항목을 가져옵니다.

선택적으로 일치하는 그룹의 하위 집합을 검색할 수 있습니다.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") == dynamic(["123", "567", "789"])
```

**구문**

`extract_all(`*정규식* `,` [*캡처 그룹*`,`] *텍스트*`)`

**인수**

* *정규식*: [정규식](./re2.md). 정규식에는 하나 이상의 캡처 그룹이 있어야 하며 16개 캡처 그룹보다 적거나 같아야 합니다.
* *캡처 그룹*: (선택 사항). 추출할 캡처 그룹을 나타내는 동적 배열 상수입니다. 유효한 값은 정규식에서 1에서 캡처 그룹 수까지입니다. 명명된 캡처 그룹도 허용됩니다(예제 섹션 참조).
* *텍스트*: `string` 검색할 수 있습니다.

**반환**

*정규식에서* 일치하는 항목을 *텍스트로*찾은 경우 : 표시된 캡처 그룹 *captureGroups(또는* *정규식의*모든 캡처 그룹)에 대한 모든 일치 항목을 포함하는 동적 배열을 반환합니다.
*캡처 그룹* 수가 1인 경우 반환된 배열에는 일치하는 값의 단일 차원이 있습니다.
*캡처 그룹* 수가 1보다 많은 경우: 반환된 배열은 *captureGroups* 선택당 다중 값 일치의 2차원 컬렉션입니다(또는 *captureGroups가* 생략된 경우 *정규식에* 있는 모든 캡처 그룹) 

일치하지 않는 경우: `null`. 

**예**

### <a name="extracting-single-capture-group"></a>단일 캡처 그룹 추출
아래 예제에서는 GUID의 헥스 바이트 표현(두 개의 헥스 숫자)을 반환합니다.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82","b8",""",,"2d","",,"2d","a7","4b","4b",",,"8f","63","24","26","","d3","14","49"]|

### <a name="extracting-several-capture-groups"></a>여러 캡처 그룹 추출 
다음 예제에서는 3개의 캡처 그룹이 있는 정규식을 사용하여 각 GUID 부분을 첫 글자, 마지막 문자 및 중간에 있는 모든 항목으로 나눕니다.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[[["8","2b8be2","d","d",],["d","fa","7","7","7","",,"bd","1","1",],["8","f6","3",],["2","4ad26d3144","9"]]|

### <a name="extracting-subset-of-capture-groups"></a>캡처 그룹의 하위 집합 추출

다음 예제에서는 캡처 그룹의 하위 집합을 선택하는 방법을 보여 주며, 이 경우 정규식은 첫 글자, 마지막 문자 및 나머지 모든 문자와 일치하며 *captureGroups* 매개 변수는 첫 번째 및 마지막 부분만 선택하는 데 사용됩니다. 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8","d"],["d",7"],["4","1",],["8","3"],["["2","9"]]|


### <a name="using-named-capture-groups"></a>명명된 캡처 그룹 사용

extract_all()에서 명명된 RE2 캡처 그룹을 활용할 수 있습니다. 아래 예제에서 *captureGroups는* 캡처 그룹 인덱스와 명명된 캡처 그룹 참조를 모두 사용하여 일치하는 값을 가져옵니다.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[[["8","2b8be2","d","d",],["d","fa","7","7","7","",,"bd","1","1",],["8","f6","3",],["2","4ad26d3144","9"]]|