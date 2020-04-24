---
title: 사용자 분석 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 구문 분석 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: beb51ac80810e5d14013e9b3571d759bb7b09125
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511512"
---
# <a name="parse-operator"></a>parse 연산자

문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 구문 분석되지 않은 문자열의 경우 계산된 열에는 null이 있습니다.
구문 [분석된 문자열을](parsewhereoperator.md) 필터링하는 구문 분석 연산자(구문 분석)를 참조하십시오.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**구문**

*T* `| parse` `kind=regex` [`flags=regex_flags`[`simple` [ [ | | *Expression* `with` *ColumnName* `:` *ColumnType* `*`*StringConstant* ] 식 `*` (문자열 상수 열 이름 [ 열 유형 ]) ... `relaxed`

**인수**

* *T*: 입력 테이블입니다.
* 종류: 

    * 단순(기본값) : StringConstant는 일반 문자열 값이며 일치는 엄격하므로 모든 문자열 구분 기호가 구문 분석 된 문자열에 나타나고 모든 확장 된 열이 필요한 형식과 일치해야합니다.
        
    * 정규선 : StringConstant는 정규식일 수 있으며 일치는 엄격하므로 모든 문자열 구분기호(이 모드의 정규식일 수 있음)는 구문 분석된 문자열에 나타나고 모든 확장된 열이 필요한 형식과 일치해야 함을 의미합니다.
    
    * `U` 플래그 : [RE2](re2.md)플래그에서 (Ungreedy), `m` (다중 라인 `s` 모드), (새 줄 `\n`일치), `i` (대/소문자를 구분하지 않음) 등과 같은 정규식 모드에서 사용할 플래그입니다.
        
    * 완화 : StringConstant는 일반 문자열 값이며 일치는 모든 문자열 구분 기호가 구문 분석 된 문자열에 나타나야하지만 확장 된 열이 필요한 형식과 부분적으로 일치 할 수 있음을 의미합니다 (필요한 형식과 일치하지 않는 확장 된 열은 값 null을 얻습니다).

* *식*: 문자열을 평가하는 식입니다.

* *열 이름:* 값을 할당할 열의 이름(문자열 식에서 가져온 값)입니다. 
  
* *ColumnType:* 값을 변환하는 형식을 나타내는 선택적 스칼라 유형이어야 합니다(기본적으로 문자열 유형).

**반환**

연산자에게 제공되는 열 목록에 따라 확장된 입력 테이블입니다.

**팁**

* 일부 [`project`](projectoperator.md) 열을 삭제하거나 이름을 바꾸려는 경우 사용합니다.

* 정크 값을 건너뛰려면 패턴에서 *를 사용합니다(문자열 열 이후에는 사용할 수 없습니다).

* 구문 분석 패턴은 *ColumnName으로* 시작하고 *StringConstant*. 

* 구문 분석된 *식이* 형식 문자열이 아닌 경우 문자열 유형으로 변환됩니다.

* 정규식 모드를 사용하는 경우 구문 분석에 사용되는 전체 정규식을 제어하기 위해 정규식 플래그를 추가하는 옵션이 있습니다.

* 정규식 모드에서 구문 분석은 패턴을 정규식으로 변환하고 [RE2 구문을](re2.md) 사용하여 내부적으로 처리되는 번호가 매겨진 캡처된 그룹을 사용하여 일치를 수행합니다.
  예를 들어 이 구문 문 :
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    내부적으로 구문 분석에서 생성되는 정규식은 `.*?<regex1>(.*?)<regex2>(\-\d+)`.
        
    - `*``.*?`로 번역되었습니다.
        
    - `string``.*?`로 번역되었습니다.
        
    - `long``\-\d+`로 번역되었습니다.

**예**

연산자는 `parse` 동일한 `extend` `extract` `string` 식에서 여러 응용 프로그램을 사용하여 테이블에 대한 간소화된 방법을 제공합니다.
이 기능은 테이블에 `string` 개발자 추적("/"`printf``Console.WriteLine`") 문으로 생성된 열과 같이 개별 열에 침입하려는 여러 값이 포함된 열이 있는 경우에 가장 유용합니다.

아래 예제에서는 테이블 `EventText` `Traces` 열에 양식의 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`문자열이 포함되어 있다고 가정합니다.
아래 작업은 6개의 열로 테이블을 `resourceName` 확장합니다. `lockTime ` `releaseTime` `previouLockTime` `Month` `Day` `totalSlices` `sliceNumber` 


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
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|토탈 슬라이스|슬라이스 번호|잠금 시간|릴리즈 타임|프리비우록타임|
|---|---|---|---|---|---|
|파이프라인스케줄러|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|파이프라인스케줄러|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|파이프라인스케줄러|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|파이프라인스케줄러|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|파이프라인스케줄러|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

정규식 모드의 경우 :

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
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|슬라이스 번호|잠금 시간|릴리즈 타임|프리비우록타임|
|---|---|---|---|---|
|파이프라인스케줄러|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|파이프라인스케줄러|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|파이프라인스케줄러|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|파이프라인스케줄러|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|파이프라인스케줄러|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


정규식 플래그를 사용하는 정규식 모드의 경우:

리소스를 가져오는 데 관심이 있는 경우Name만 이 쿼리를 사용합니다.

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
|파이프라인스케줄러, totalSlices=27, 슬라이스넘버=23, lockTime=02/17/2016 08:40:01, 릴리즈타임=02/17/2016 08:40:01|
|파이프라인스케줄러, totalSlices=27, 슬라이스넘버=15, lockTime=02/17/2016 08:40:00, 릴리즈타임=02/17/2016 08:40:00|
|파이프라인스케줄러, totalSlices=27, 슬라이스넘버=20, lockTime=02/17/2016 08:40:01, 릴리즈타임=02/17/2016 08:40:01|
|파이프라인스케줄러, totalSlices=27, 슬라이스넘버=22, lockTime=02/17/2016 08:41:01, 릴리즈타임=02/17/2016 08:41:00|
|파이프라인스케줄러, totalSlices=27, 슬라이스넘버=16, lockTime=02/17/2016 08:41:00, 릴리즈타임=02/17/2016 08:41:00|

그러나 기본 모드가 욕심이기 때문에 예상된 결과를 얻지 못합니다.
또는 리소스이름이 때때로 소문자로 표시되는 레코드가 거의 없더라도 일부 값에 대해 null을 얻을 수 있습니다.
원하는 결과를 얻으려면 욕심이 없는 ()`U`및 대/소문자 구분()`i`정규식 플래그를 사용하지 않도록 설정하여 이 결과를 실행할 수 있습니다.

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
|파이프라인스케줄러|
|파이프라인스케줄러|
|파이프라인스케줄러|
|파이프라인스케줄러|
|파이프라인스케줄러|


구문 분석 된 문자열에 줄 바호가 `s` 있는 경우 플래그를 사용하여 텍스트를 예상대로 구문 분석해야 합니다.

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

|resourceName|토탈 슬라이스|잠금 시간|릴리즈 타임|이전 잠금 시간|
|---|---|---|---|---|
|파이프라인스케줄러<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|파이프라인스케줄러<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|파이프라인스케줄러<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|파이프라인스케줄러<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|파이프라인스케줄러<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


완화 모드에 대 한이 예제에서: totalSlices 확장 된 열 형식 긴 해야 하지만 구문 분석 된 문자열에 비ValidLongValue 값을 가있습니다.
releaseTime 확장 열에는 동일한 문제가 있으며 비ValidDateTime 값은 날짜 시간으로 구문 분석할 수 없습니다.
이 경우 이 두 확장 된 열은 값 null을 얻고 다른 열 (예 : sliceNumber)은 여전히 올바른 값을 가져옵니다.

kind = 아래 동일한 쿼리에 대해 간단한 사용하여 확장 된 열에 엄격하기 때문에 모든 확장 된 열에 대해 null을 제공합니다 (즉 완화 모드에서 완화 모드와 단순 모드의 차이, 확장 된 열을 부분적으로 일치시킬 수 있음).

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
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|토탈 슬라이스|슬라이스 번호|잠금 시간|릴리즈 타임|프리비우록타임|
|---|---|---|---|---|---|
|파이프라인스케줄러|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|파이프라인스케줄러|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|파이프라인스케줄러||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|파이프라인스케줄러||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|파이프라인스케줄러|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|