---
title: pack ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 pack ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94629ae8f4795e28cbfb0c41f06596731cdd8d9
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717328"
---
# <a name="pack"></a>pack()

`dynamic`이름 및 값 목록에서 개체 (속성 모음)를 만듭니다.

함수에 대 한 별칭 `pack_dictionary()` 입니다.

**구문**

`pack(`*key1* `,` *value1* `,` *key2* `,` *value2*`,... )`

**인수**

* 키와 값의 교대로 반복 되는 목록입니다. 목록의 전체 길이는 짝수 여야 합니다.
* 모든 키는 비어 있지 않은 상수 문자열 이어야 합니다.

**예**

다음 예제는 `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`을 반환합니다.

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

2 개의 테이블, SmsMessages 및 MmsMessages를 사용 합니다.

테이블 SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

테이블 MmsMessages 

|SourceNumber |TargetNumber| AttachmentSize | 첨부 파일 유형이 | AttachmentName
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | jpeg | Pic1
|555-555-1234 |555-555-1212 | 250 | jpeg | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

다음 쿼리:
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmentSize", AttachmentSize, "AttachmentType", AttachmentType, "AttachmentName", AttachmentName))
| where SourceNumber == "555-555-1234"
``` 

HRESULT = NO_ERROR를

|TableName |SourceNumber |TargetNumber | 채워집니다
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"CharsCount": 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"CharsCount": 50}
|MmsMessages|555-555-1234 |555-555-1212 | {"AttachmentSize": 250, "첨부 파일 유형이": "jpeg", "AttachmentName": "Pic2"}
|MmsMessages|555-555-1234 |555-555-1213 | {"AttachmentSize": 300, "첨부 파일 유형이": "png", "AttachmentName": "Pic3"}
