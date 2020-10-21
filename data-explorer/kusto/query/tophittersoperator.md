---
title: hitters 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 hitters 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d95c981f999d0842a266702ad5fc733281d45a7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245787"
---
# <a name="top-hitters-operator"></a>top-hitters 연산자

입력의 불균형 분포를 가정 하 고 처음 *N 개* 결과의 근사값을 반환 합니다.

```kusto
T | top-hitters 25 of Page by Views 
```

> [!NOTE]
> `top-hitters` 는 근사값 알고리즘 이며, 많은 데이터를 사용 하 여 실행할 때 사용 해야 합니다. 최상위 hitters의 근사값은 [개수-최소 스케치](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch) 알고리즘을 기반으로 합니다.  

## <a name="syntax"></a>구문

*T* `| top-hitters` *numberofrows* `of` *sort_key* `[` `by` *식*`]`

## <a name="arguments"></a>인수

* *Numberofrows*: 반환할 *T* 의 행 수입니다. 임의의 숫자 식을 지정할 수 있습니다.
* *sort_key*: 행을 정렬 하는 기준이 되는 열의 이름입니다.
* *expression*: (선택 사항) hitters 예측에 사용 되는 식입니다. 
    * *식*: hitters는 최대 합계 (*식*)를 포함 하는 *numberofrows* 행을 반환 합니다. Expression은 열 이거나 숫자로 계산 되는 다른 식일 수 있습니다. 
    *  *Expression* 을 언급 하지 않을 경우 hitters 알고리즘은 *정렬 키*의 발생 횟수를 계산 합니다.  

## <a name="examples"></a>예

### <a name="get-most-frequent-items"></a>가장 자주 항목 가져오기 

다음 예제에서는 위키백과에서 대부분의 페이지를 포함 하는 최고 수준의 5 개 언어를 찾는 방법을 보여 줍니다 (4 월 2016 일에 액세스 한 후에 액세스). 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|언어|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

### <a name="get-top-hitters-based-on-column-value"></a>열 값을 기준으로 상위 hitters 가져오기

다음 예제에서는 2016 년의 위키백과에서 가장 많이 표시 되는 영어 페이지를 찾는 방법을 보여 줍니다. 쿼리에서는 ' Views ' (정수 숫자)를 사용 하 여 페이지 인기도 (뷰 수)를 계산 합니다. 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|페이지|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|Java_ (programming_language)|16489491|
|United_States|13928841|
|위키백과|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film)|10714263|
|Star_Wars: _The_Force_Awakens|9770653|
|포털: Current_events|9578000|
