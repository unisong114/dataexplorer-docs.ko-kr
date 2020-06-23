---
title: parse 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구문 분석 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd70b2135a485303cbf52d984e0b406052c4023a
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264950"
---
# <a name="parse-operator"></a>parse 연산자

문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 구문 분석 되지 않은 문자열의 경우 계산 열에 null이 포함 됩니다.
자세한 내용은 [구문 분석-where 연산자](parsewhereoperator.md)를 참조 하세요.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**구문**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *식* `with` `*` (*stringconstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**인수**

* *T*: 입력 테이블입니다.
* 종류로

    * simple (기본값): StringConstant는 일반 문자열 값 이며 일치 항목은 strict입니다. 모든 문자열 구분 기호가 구문 분석 된 문자열에 표시 되어야 하 고 모든 확장 된 열이 필요한 형식과 일치 해야 합니다.
        
    * regex: StringConstant는 정규식 일 수 있으며, 일치 항목은 strict입니다. 이 모드에 대 한 regex 일 수 있는 모든 문자열 구분 기호는 구문 분석 된 문자열에 표시 되어야 하며 모든 확장 된 열이 필요한 형식과 일치 해야 합니다.
    
    * flags: `U` RE2 flags에서 (ungreedy), ( `m` 여러 줄 모드), `s` (새 줄 일치), ( `\n` `i` 대/소문자 구분 안 [RE2 flags](re2.md)함)와 같은 regex 모드에서 사용할 플래그입니다.
        
    * 완화 됨: StringConstant는 일반 문자열 값 이며 일치는 완화 됩니다. 모든 문자열 구분 기호가 구문 분석 된 문자열에 표시 되어야 하지만 확장 된 열이 필요한 형식과 부분적으로 일치할 수 있습니다. 필요한 유형과 일치 하지 않는 확장 열은 null 값을 가져옵니다.

* *Expression*: 문자열로 계산 되는 식입니다.

* *ColumnName:* 문자열 식에서 추출 된 값을 할당할 열의 이름입니다. 
  
* *ColumnType:* 필드. 값을 변환할 대상 형식을 나타내는 스칼라 값입니다. 기본값은 `string` 형식입니다.

**반환**

입력 테이블은 연산자에 제공 되는 열 목록에 따라 확장 됩니다.

**팁**

* [`project`](projectoperator.md)일부 열을 삭제 하거나 이름을 변경 하려는 경우에 사용 합니다.

* 정크 값을 건너뛰려면 패턴에서 *를 사용 합니다. 

    > [!NOTE] 
    > 은 `*` 유형 열 뒤에 사용할 수 없습니다 `string` .

* Parse 패턴은 *Stringconstant*를 사용 하는 것이 아니라 *ColumnName* 으로 시작 될 수 있습니다.

* 구문 분석 된 *식이* 형식이 아니면 `string` 형식으로 변환 됩니다 `string` .

* Regex 모드를 사용 하는 경우 구문 분석에 사용 되는 전체 regex를 제어 하는 regex 플래그를 추가 하는 옵션이 있습니다.

* Regex 모드에서 parse는 패턴을 regex로 변환 합니다. [RE2 구문을](re2.md) 사용 하 여 일치를 수행 하 고 내부적으로 처리 되는 번호가 매겨진 캡처된 그룹을 사용 합니다.
    예를 들어:

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Parse 문에서는 구문 분석에 의해 내부적으로 생성 되는 regex가 `.*?<regex1>(.*?)<regex2>(\-\d+)` 입니다.
        
    * `*`가로 변환 되었습니다 `.*?` .
        
    * `string`가로 변환 되었습니다 `.*?` .
        
    * `long`가로 변환 되었습니다 `\-\d+` .

**예**

`parse`연산자는 `extend` 동일한 식에서 여러 응용 프로그램을 사용 하 여 테이블에 간소화 된 방법을 제공 `extract` `string` 합니다. 이 결과는 테이블에 `string` 개별 열로 나눌 여러 값이 포함 된 열이 있는 경우에 유용 합니다. 예를 들어 개발자 추적 (" `printf` "/"") 문에 의해 생성 된 열입니다 `Console.WriteLine` .

아래 예에서는 테이블의 열에 형식의 `EventText` `Traces` 문자열이 포함 되어 있다고 가정 합니다 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
이 작업을 수행 하면 테이블에,,,,,, `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previousLockTime` `Month` 및 `Day` 열이 6 개까지 확장 됩니다. 

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

**Regex 모드의 경우**

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

**Regex 플래그를 사용 하는 regex 모드의 경우**

Context.resourcename을 가져오는 데 관심이 있는 경우 다음 쿼리를 사용 합니다.

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
|PipelineScheduler, totalSlices = 27, sliceNumber = 23, lockTime = 02/17/2016 08:40:01, releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler, totalSlices = 27, sliceNumber = 15, lockTime = 02/17/2016 08:40:00, releaseTime = 02/17/2016 08:40:00|
|PipelineScheduler, totalSlices = 27, sliceNumber = 20, lockTime = 02/17/2016 08:40:01, releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler, totalSlices = 27, sliceNumber = 22, lockTime = 02/17/2016 08:41:01, releaseTime = 02/17/2016 08:41:00|
|PipelineScheduler, totalSlices = 27, sliceNumber = 16, lockTime = 02/17/2016 08:41:00, releaseTime = 02/17/2016 08:41:00|

기본 모드가 greedy 이기 때문에 예상 되는 결과를 얻을 수 없습니다.
몇 개의 레코드가 있는 경우 (예를 들어, 경우에 따라 대/소문자가 소문자로 표시 *되는 경우* ) 일부 값에 대해 null을 얻을 수 있습니다.

원하는 결과를 얻으려면 non-greedy를 사용 하 여 쿼리를 실행 하 `U` 고 대/소문자를 구분 하는 regex 플래그를 사용 하지 않도록 설정 합니다 `i` .

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

구문 분석 된 문자열에 줄바꿈 있으면 플래그를 사용 `s` 하 여 텍스트를 구문 분석 합니다.

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

**완화 모드**

완화 된 모드에 대 한이 예제에서 *totalslices* 확장 열은 형식 이어야 합니다 `long` . 그러나 구문 분석 된 문자열에는 *nonValidLongValue*값이 있습니다.
*Releasetime* 확장 열에서 *비 유효 날짜/* 시간 값은 *datetime*으로 구문 분석할 수 없습니다.
이러한 두 개의 확장 된 열은 null 값을 얻게 되 고 다른 값 (예: *sliceNumber*)은 여전히 올바른 값을 가져옵니다.

아래와 동일한 쿼리에 옵션 *kind = simple* 을 사용 하는 경우 모든 확장 열에 대해 null을 얻게 됩니다. 이 옵션은 확장 열에 대해 엄격 하며 완화 모드와 단순 모드의 차이입니다.

 > [!NOTE] 
 > 완화 된 모드에서는 확장 열이 부분적으로 일치할 수 있습니다.

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
 
