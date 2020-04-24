---
title: 팩() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 pack()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7b43be96f272ab929434f10cac910bd4072e650
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511835"
---
# <a name="pack"></a>pack()

이름과 `dynamic` 값 목록에서 개체(속성 가방)를 만듭니다.

기능할 `pack_dictionary()` 별칭입니다.

**구문**

`pack(`*key1* `,` *value1* `,` *key2* `,` *값2*`,... )`

**인수**

* 키와 값의 교대 목록(목록의 총 길이가 짝수여야 함)
* 모든 키는 비어 없는 상수 문자열이어야 합니다.

**예**

다음 예제는 `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`을 반환합니다.

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

2 테이블, SmsMessages 및 MmsMessages를 취할 수 있습니다 :

테이블 SmsMessages 

|소스 번호 |대상 번호| 차스카운트
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

테이블 MmsMessages 

|소스 번호 |대상 번호| 어태치먼트엠넷사이즈 | 어태치먼트엠타입 | 첨부 망 이름
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | JPEG | 그림 1
|555-555-1234 |555-555-1212 | 250 | JPEG | 그림 2
|555-555-1234 |555-555-1213 | 300 | png | 그림 3

다음 쿼리:
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmnetSize", AttachmnetSize, "AttachmnetType", AttachmnetType, "AttachmnetName", AttachmnetName))
| where SourceNumber == "555-555-1234"
``` 

HRESULT = NO_ERROR를

|TableName |소스 번호 |대상 번호 | 점심
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"차스카운트": 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"차스카운트": 50}
|MmsMessages|555-555-1234 |555-555-1212 | {"AttachmnetSize": 250, "AttachmnetType": "jpeg", "AttachmnetName": "Pic2"}
|MmsMessages|555-555-1234 |555-555-1213 | {"AttachmnetSize": 300, "AttachmnetType": "png", "AttachmnetName": "Pic3"}