---
title: . 데이터베이스 스크립트 실행-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 스크립트 실행 기능을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: c8fa3a000de67559c83745c598da40797e31f9b9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248341"
---
# <a name="execute-database-script"></a>.execute database script

단일 데이터베이스 범위에서 제어 명령의 일괄 처리를 실행 합니다.

## <a name="syntax"></a>구문

`.execute` `database` `script`  
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]   
`<|`  
 *컨트롤-명령-스크립트*

### <a name="parameters"></a>매개 변수

* *컨트롤-명령-스크립트*: 하나 이상의 컨트롤 명령을 포함 하는 텍스트입니다.
* *데이터베이스 범위*: 스크립트는 요청 컨텍스트의 일부로 지정 된 *데이터베이스 범위* 에 적용 됩니다.

### <a name="optional-properties"></a>선택적 속성

| 속성            | Type            | 설명                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `ContinueOnErrors`            | `bool`        | 로 설정 `false` 되 면 첫 번째 오류 발생 시 스크립트가 중지 됩니다. 로 설정 하면 `true` 스크립트 실행이 계속 됩니다. 기본값: `false`. |

## <a name="output"></a>출력

스크립트에 표시 되는 각 명령은 출력 테이블에 별도의 레코드로 보고 됩니다. 각 레코드에는 다음과 같은 필드가 있습니다.

|출력 매개 변수 |Type |설명
|---|---|--- 
|OperationId  |Guid |명령의 식별자입니다.
|CommandType  |String |명령의 유형입니다.
|CommandText  |String |특정 명령의 텍스트입니다.
|결과|String|특정 명령 실행의 결과입니다.
|이유|String|명령 실행 결과에 대 한 자세한 정보입니다.

>[!NOTE]
>* 스크립트 텍스트는 명령 사이에 빈 줄과 주석을 포함할 수 있습니다.
>* 명령은 입력 스크립트에 표시 되는 순서 대로 순차적으로 실행 됩니다.
>* 스크립트 실행은 비트랜잭션 이며 오류 발생 시 롤백이 수행 되지 않습니다. 을 사용 하는 경우 idempotent 형식의 명령을 사용 하는 것이 좋습니다 `.execute database script` .
>* 명령의 기본 동작은 첫 번째 오류에서 실패 합니다. 속성 인수를 사용 하 여 변경할 수 있습니다.
>* 읽기 전용 제어 명령 (. show commands)은 실행 되지 않으며 상태와 함께 보고 됩니다 `Skipped` .

## <a name="example"></a>예제

```kusto
.execute database script <|
//
// Create tables
.create-merge table T(a:string, b:string)
//
// Apply policies
.alter-merge table T policy retention softdelete = 10d 
//
// Create functions
.create-or-alter function
  with (skipvalidation = "true") 
  SampleT1(myLimit: long) { 
    T1 | limit myLimit
}
```

|OperationId|CommandType|CommandText|결과|이유|
|---|---|---|---|---|
|1d28531b-58c8-4023-a5d3-16fa73c06cfa|TableCreate|. create-merge 테이블 T (a:string, b:string)|완료됨||
|67d0ea69-baa4-419a-93d3-234c03834360|보존 정책 변경|. alter-merge table T 정책 보존 소프트 삭제 = now-10d|완료됨||
|0b0e8769-d4e8-4ff9-adae-071e52a650c7|FunctionCreateOrAlter|.create-or-alter function<br>with (skipvalidation = "true")<br>SampleT1 (myLimit: long) {<br>T1 \| 제한 myLimit<br>}|완료됨||
