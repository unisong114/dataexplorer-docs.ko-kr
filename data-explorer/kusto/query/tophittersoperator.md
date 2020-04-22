---
title: 최상위 타자 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 최상위 타자 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7105bd4f9818deab1f0fa5dbe25dbb2d5b1b4afc
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663117"
---
# <a name="top-hitters-operator"></a>top-hitters 연산자

첫 번째 *N* 결과의 근사치를 반환합니다(입력의 비뚤어진 분포를 가정).

```kusto
T | top-hitters 25 of Page by Views 
```

**구문**

*T* `| top-hitters` *sort_key* `[` *번호sort_key표현* `of` `by` *expression*`]`

**인수**

* *NumberOfRows*: 반환할 *T행의* 수입니다. 숫자 식을 지정할 수 있습니다.
* *sort_key*: 행을 정렬할 열의 이름입니다.
* *식*: (선택 사항) 최상위 타자 추정에 사용되는 식입니다. 
    * *표현식*: 최상위 타자는 대략적인 최대*합계(표현식)가*있는 *NumberOfRows* 행을 반환합니다. 식은 열이거나 숫자로 평가하는 다른 식일 수 있습니다. 
    *  *식을* 언급하지 않으면 최상위 타자 알고리즘은 *정렬 키의*발생을 계산합니다.  

**참고 사항**

`top-hitters`는 근사 알고리즘이며 큰 데이터로 실행할 때 사용해야 합니다. 탑 타자의 근사치는 [카운트-최소-스케치](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch) 알고리즘을 기반으로 합니다.  

**예제**

## <a name="getting-top-hitters-most-frequent-items"></a>최고 타자 얻기 (가장 빈번한 항목) 

다음 예제에서는 위키백과에서 대부분의 페이지가 있는 상위 5개 언어를 찾는 방법을 보여 주며(2016년 4월 이후에 액세스). 

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

## <a name="getting-top-hitters-based-on-column-value-"></a>최고 타자 얻기 (열 값에 따라) ***

다음 예제에서는 2016년 위키백과에서 가장 많이 본 영어 페이지를 찾는 방법을 보여 주며, 이 에 대한 자세한 내용은 다음과 같은 설명입니다. 쿼리는 '조회수'(정수 번호)를 사용하여 페이지 인기도(조회수)를 계산합니다. 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|호출|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|Java_(programming_language)|16489491|
|United_States|13928841|
|키 피 디 아|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_(2015_film)|10714263|
|Star_Wars:_The_Force_Awakens|9770653|
|포털:Current_events|9578000|