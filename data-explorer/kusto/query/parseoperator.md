---
title: parse 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 parse 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: a9da3735df9299b387188157bbae3d561f5de631
ms.sourcegitcommit: f20619fac91f9bb2e6507cac10d41fb8425218e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97811772"
---
# <a name="parse-operator"></a>parse 연산자

문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 구문 분석되지 않은 문자열의 경우 계산 열에 null이 포함됩니다. 구문 분석에 실패한 행을 사용하지 않아도 되는 경우 [parse-where 연산자](parsewhereoperator.md)를 사용하는 것이 좋습니다.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>Syntax

*T* `| parse` [`kind=regex` [`flags=regex_flags`] |`simple`|`relaxed`] *Expression* `with` `*` (*StringConstant* *ColumnName* [`:` *ColumnType*]) `*`...

## <a name="arguments"></a>인수

* *T*: 입력 테이블입니다.
* kind:

    * simple(기본값): StringConstant는 일반 문자열 값이며, 일치가 엄격합니다. 모든 문자열 구분 기호가 구문 분석된 문자열에 표시되어야 하고, 모든 확장 열이 필요한 형식과 일치해야 합니다.
        
    * regex: StringConstant는 정규식일 수 있으며, 일치가 엄격합니다. 이 모드에 대한 regex일 수 있는 모든 문자열 구분 기호가 구문 분석된 문자열에 표시되어야 하고, 모든 확장 열이 필요한 형식과 일치해야 합니다.
    
    * flags: [RE2 플래그](re2.md)에서 `U`(Ungreedy), `m`(여러 줄 모드), `s`(줄 바꿈(`\n`) 일치), `i`(대/소문자 구분 안 함)와 같은 regex 모드에서 사용할 플래그입니다.
        
    * relaxed: StringConstant는 일반 문자열 값이며, 일치가 느슨합니다. 모든 문자열 구분 기호가 구문 분석된 문자열에 표시되어야 하지만, 확장 열이 필요한 형식과 부분적으로 일치할 수 있습니다. 필요한 형식과 일치하지 않는 확장 열은 null 값을 얻습니다.

* *식*: 문자열로 계산되는 식입니다.

* *ColumnName:* 문자열 식에서 추출된 값을 할당할 열의 이름입니다. 
  
* *ColumnType:* (선택 사항) 값을 변환할 형식을 나타내는 스칼라 값입니다. 기본값은 `string` 형식입니다.

## <a name="returns"></a>반환

연산자에 제공되는 열 목록에 따라 확장되는 입력 테이블입니다.

**팁**

* 일부 열을 삭제하거나 이름을 변경하려면 [`project`](projectoperator.md)를 사용합니다.

* 정크 값을 건너뛰려면 패턴에서 *를 사용합니다. 

    > [!NOTE] 
    > `*`는 `string` 형식 열 뒤에 사용할 수 없습니다.

* 구문 분석 패턴은 *StringConstant* 뿐만 아니라 *ColumnName* 으로도 시작할 수 있습니다.

* 구문 분석된 *Expression* 이 `string` 형식이 아니면 `string` 형식으로 변환됩니다.

* regex 모드를 사용하는 경우 parse에서 사용되는 전체 regex를 제어하는 regex 플래그를 추가하는 옵션이 있습니다.

* regex 모드에서 parse는 패턴을 regex로 변환합니다. [RE2 구문](re2.md)을 사용하여 일치를 수행하고, 내부적으로 처리되는 번호가 매겨진 캡처된 그룹을 사용합니다.
    다음은 그 예입니다. 

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    parse 문에서 구문 분석을 통해 내부적으로 생성되는 regex는 `.*?<regex1>(.*?)<regex2>(\-\d+)`입니다.
        
    * `*`가 `.*?`로 변환되었습니다.
        
    * `string`이 `.*?`로 변환되었습니다.
        
    * `long`이 `\-\d+`로 변환되었습니다.

## <a name="examples"></a>예제

`parse` 연산자는 동일한 `string` 식에서 여러 `extract` 애플리케이션을 사용하여 테이블을 `extend`(확장)하는 효율적인 방식을 제공합니다. 이 결과는 테이블에 개별 열로 분할하려는 여러 값이 포함된 `string` 열이 있는 경우에 유용합니다. 예를 들어 개발자 추적("`printf`"/"`Console.WriteLine`") 문으로 생성된 열이 있습니다.

아래 예제에서는 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` 형식의 문자열이 `Traces` 테이블의 `EventText` 열에 포함되어 있다고 가정합니다.
이 작업을 수행하면 테이블이 6개의 열(`resourceName`, `totalSlices`, `sliceNumber`, `lockTime `, `releaseTime` 및 `previousLockTime`)로 확장됩니다. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**regex 모드의 경우**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|

**regex 플래그를 사용하는 regex 모드의 경우**

resourceName만 가져오는 데 관심이 있는 경우 다음 쿼리를 사용합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00|
|PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00|
|PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00|

기본 모드가 greedy이므로 예상 결과를 얻을 수 없습니다.
*resourceName* 이 때로는 소문자로 표시되고 때로는 대문자로 표시되는 몇 개의 레코드가 있는 경우 일부 값에 대해 null을 얻을 수 있습니다.

원하는 결과를 얻으려면 non-greedy(`U`)를 사용하여 쿼리를 실행하고 대/소문자 구분 `i` regex 플래그를 사용하지 않도록 설정합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|

구문 분석된 문자열에 줄 바꿈이 있는 경우 `s` 플래그를 사용하여 텍스트를 구문 분석합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|totalSlices|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**relaxed 모드**

relaxed 모드에 대한 다음 예제에서는 *totalSlices* 확장 열이 `long` 형식이어야 합니다. 그러나 구문 분석된 문자열에는 *nonValidLongValue* 값이 있습니다.
*releaseTime* 확장 열에서 *nonValidDateTime* 값은 *datetime* 으로 구문 분석할 수 없습니다.
이러한 두 확장 열은 null 값을 얻지만, *sliceNumber* 와 같은 열은 여전히 올바른 값을 얻습니다.

*kind = simple* 옵션을 아래의 동일한 쿼리에 사용하는 경우 모든 확장 열에 대해 null을 얻게 됩니다. 이 옵션은 확장 열에서 엄격하며, relaxed 모드와 simple 모드의 차이입니다.

 > [!NOTE] 
 > relaxed 모드에서는 확장 열을 부분적으로 일치시킬 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
 
