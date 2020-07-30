---
title: 구문 분석-where operator-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구문 분석-where 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: c2936ec7461850aaad6fdb4e9daa7624dd561c49
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346255"
---
# <a name="parse-where-operator"></a>parse-where 연산자

문자열 식을 계산 하 고 해당 값을 하나 이상의 계산 된 열로 구문 분석 합니다. 결과는 성공적으로 구문 분석 된 문자열입니다. 

구문 분석 되지 않은 문자열에 대해 null을 생성 하는 [parse 연산자](parseoperator.md)를 참조 하세요.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>구문

*T* `| parse-where` [ `kind=regex` [ `flags=regex_flags` ] | `simple` ] *식* `with` `*` (*stringconstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

## <a name="arguments"></a>인수

* *T*: 입력 테이블입니다.

* *종류*: 

    * *simple* (기본값): stringconstant는 일반 문자열 값 이며 일치 항목은 strict입니다. 모든 문자열 구분 기호가 구문 분석 된 문자열에 표시 되어야 하 고 모든 확장 된 열이 필요한 형식과 일치 해야 합니다.
        
    * *regex*: stringconstant는 정규식 일 수 있으며, 일치 항목은 strict입니다. 모든 문자열 구분 기호가 구문 분석 된 문자열에 표시 되어야 하 고 모든 확장 된 열이 필요한 형식과 일치 해야 합니다. 문자열 구분 기호는이 모드에 대 한 regex 일 수 있습니다.
    
    * *flags*: regex 모드에서 사용할 플래그: `U` (ungreedy), `m` (여러 줄 모드), `s` (새 줄 일치), ( `\n` `i` 대/소문자 구분 안 함) [RE2 플래그](re2.md)에서 더 많은 플래그를 찾을 수 있습니다.
        
* *Expression*: 문자열로 계산 되는 식입니다.

* *ColumnName:* 문자열 식에서 가져온 값에 할당 된 열의 이름입니다. 
  
* *ColumnType:* 값을 변환할 형식을 나타내는 선택적 스칼라 형식 이어야 합니다. 기본값은 문자열 형식입니다.

## <a name="returns"></a>반환

입력 테이블은 연산자에 제공 되는 열 목록에 따라 확장 됩니다.

> [!Note] 
> 성공적으로 구문 분석 된 문자열만 출력에 나타납니다. 패턴과 일치 하지 않는 문자열은 필터링 됩니다.

**팁**

* `parse-where`는 [구문](parseoperator.md)분석과 동일한 방식으로 문자열을 구문 분석 하 고 성공적으로 구문 분석 되지 않은 문자열을 필터링 합니다.

* 일부 열을 삭제 하거나 이름을 변경 하려는 경우에도 [project](projectoperator.md) 를 사용 합니다.

* 정크 값을 건너뛰려면 패턴에서 *를 사용 합니다. 이 값은 문자열 열 뒤에 사용할 수 없습니다.

* Parse 패턴은 *Stringconstant*뿐만 아니라 *ColumnName*으로 시작 될 수 있습니다. 

* 구문 분석 된 *식이* 문자열 형식이 아닌 경우 문자열 형식으로 변환 됩니다.

* Regex 모드를 사용 하는 경우 regex 플래그를 추가 하 여 구문 분석에 사용 되는 전체 regex를 제어할 수 있습니다.

* Regex 모드에서 구문 분석은 패턴을 regex로 변환 하 고, 내부적으로 처리 되는 번호가 매겨진 캡처된 그룹을 사용 하 여 일치를 수행 하기 위해 [RE2 구문을](re2.md) 사용 합니다.
  
  예를 들어 다음 구문 분석 문은 다음과 같습니다.
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    내부적으로 구문 분석에서 생성 되는 regex는 `.*?<regex1>(.*?)<regex2>(\-\d+)` 입니다.
        
    - `*`가로 변환 되었습니다 `.*?` .
        
    - `string`가로 변환 되었습니다 `.*?` .
        
    - `long`가로 변환 되었습니다 `\-\d+` .

## <a name="examples"></a>예

`parse-where`연산자는 `extend` 동일한 식에서 여러 응용 프로그램을 사용 하 여 테이블에 간소화 된 방법을 제공 `extract` `string` 합니다. 이는 테이블에 `string` 개별 열로 나눌 여러 값이 포함 된 열이 있는 경우에 가장 유용 합니다. 예를 들어 개발자 추적 (" `printf` "/"") 문에 의해 생성 된 열을 나눌 수 있습니다 `Console.WriteLine` .

### <a name="using-parse"></a>`parse` 사용

아래 예에서는 테이블의 열에 형식의 `EventText` `Traces` 문자열이 포함 되어 있습니다 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` . 아래 작업을 수행 하면 테이블에,,,,,, `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` `Month` 및 `Day` 열이 6 개 있는 것으로 확장 됩니다. 

일부 문자열에는 전체 일치 항목이 없습니다.

를 사용 하면 `parse` 계산 열에 null이 포함 됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

### <a name="using-parse-where"></a>`parse-where` 사용 

' Parse-where '를 사용 하면 결과에서 구문 분석 된 문자열이 성공적으로 필터링 되지 않습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


### <a name="regex-mode-using-regex-flags"></a>Regex 플래그를 사용 하는 regex 모드

Context.resourcename 및 totalSlices를 가져오려면 다음 쿼리를 사용 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

### <a name="parse-where-with-case-insensitive-regex-flag"></a>`parse-where`대/소문자를 구분 하지 않는 regex 플래그 사용

위의 쿼리에서 기본 모드는 대/소문자를 구분 하므로 문자열이 성공적으로 구문 분석 됩니다. 결과를 얻지 못했습니다.

필요한 결과를 얻으려면 `parse-where` 대/소문자를 구분 하지 않는 ( `i` ) regex 플래그를 사용 하 여를 실행 합니다.

세 개의 문자열만 성공적으로 구문 분석 되므로 결과는 3 개의 레코드 (일부 totalSlices는 잘못 된 정수를 보유 함)입니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex flags=i EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

|resourceName|totalSlices|
|---|---|
|PipelineScheduler|27|
|PipelineScheduler|27|
|PipelineScheduler|27|
