---
title: extract_all ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 extract_all ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94bafd3890f57a1379440c5cced3fa349f9055c5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616425"
---
# <a name="extract_all"></a>extract_all()

텍스트 문자열에서 [정규식](./re2.md) 에 대 한 모든 일치 항목을 가져옵니다.

필요에 따라 일치 하는 그룹의 하위 집합을 검색할 수 있습니다.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

**구문**

`extract_all(`*regex* `,` [*captureGroups*`,`] *텍스트*`)`

**인수**

* *regex*: [정규식](./re2.md)입니다. 정규식에는 캡처링 그룹이 하나 이상 있어야 하며, 16 개의 캡처링 그룹 보다 작거나 같아야 합니다.
* *captureGroups*: (선택 사항). 추출할 캡처 그룹을 나타내는 동적 배열 상수입니다. 유효한 값은 정규식에서 1에서 캡처링 그룹의 수입니다. 명명 된 캡처 그룹도 허용 됩니다 (예제 섹션 참조).
* *text*: `string` 검색할입니다.

**반환**

*Regex* 가 *텍스트*에서 일치 항목을 찾으면: 표시 된 캡처 그룹 *captureGroups* (또는 *regex*의 모든 캡처링 그룹)에 대 한 모든 일치 항목을 포함 하는 동적 배열을 반환 합니다.
*CaptureGroups* 수가 1 인 경우 반환 되는 배열에는 일치 하는 값의 단일 차원이 있습니다.
Number of *captureGroups* 가 1 보다 많은 경우 반환 된 배열은 *captureGroups* 선택 당 다중 값 일치 항목의 2 차원 컬렉션입니다 (또는 *captureGroups* 가 생략 된 경우 *regex* 에 있는 모든 캡처 그룹). 

와 일치 하는 항목이 없으면 `null`입니다. 

**예제**

### <a name="extracting-single-capture-group"></a>단일 캡처 그룹 추출
아래 예제에서는 GUID의 16 진수 바이트 표현 (두 자리 16 진수)을 반환 합니다.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82", "b8", "be", "2d", "df", "a7", "4b", "d1", "8f", "63", "24", "ad", "26", "d3", "14", "49"]|

### <a name="extracting-several-capture-groups"></a>여러 캡처 그룹 추출 
다음 예제에서는 3 개의 캡처링 그룹이 있는 정규식을 사용 하 여 각 GUID 부분을 첫 번째 문자, 마지막 문자 및 중간의 모든 항목으로 분할 합니다.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "fa", "7"], ["4", "bd", "1"], ["8", "f6", "3"], ["2", "4ad26d3144", "9"]]|

### <a name="extracting-subset-of-capture-groups"></a>캡처 그룹의 하위 집합 추출

다음 예제에서는 캡처링 그룹의 하위 집합을 선택 하는 방법을 보여 줍니다 .이 경우에는 정규식이 첫 번째 문자, 마지막 문자 및 모든 rest로 일치 하는 반면 *captureGroups* 매개 변수는 첫 번째 및 마지막 부분만 선택 하는 데 사용 됩니다. 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "d"], ["d", "7"], ["4", "1"], ["8", "3"], ["2", "9"]]|


### <a name="using-named-capture-groups"></a>명명 된 캡처 그룹 사용

Extract_all ()에서 RE2의 명명 된 캡처 그룹을 활용할 수 있습니다. 아래 예제에서 *captureGroups* 는 캡처 그룹 인덱스와 명명 된 캡처 그룹 참조를 모두 사용 하 여 일치 하는 값을 가져옵니다.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "fa", "7"], ["4", "bd", "1"], ["8", "f6", "3"], ["2", "4ad26d3144", "9"]]|