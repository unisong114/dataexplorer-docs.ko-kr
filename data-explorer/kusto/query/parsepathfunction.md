---
title: parse_path() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_path()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2267efb4e47a6d8e45733ad48dd9f7f4019c1fa8
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744670"
---
# <a name="parse_path"></a>parse_path()

파일 경로를 `string` 구문 분석 [`dynamic`](./scalar-data-types/dynamic.md) 하 고 경로의 다음 부분을 포함 하는 개체를 반환: 체계, 루트 Path, 디렉터리 경로, 디렉터리 이름, 파일 이름, 확장자, AlternateDataStreamName.
두 가지 유형의 슬래시가 있는 간단한 경로 외에도 스키마(예: "file://..."), 공유 경로(예: "shareddrive\users..."),\\긴 경로(예: "?\C:..."),\\대체 데이터 스트림(예: "file1.exe:file2.exe")이 있는 경로를 지원합니다.

**구문**

`parse_path(`*경로*`)`

**인수**

* *경로*: 파일 경로를 나타내는 문자열입니다.

**반환**

위에 나열된 `dynamic` 경로 구성 요소를 포함시킨 형식의 개체입니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->

```kusto
datatable(p:string) 
[
    @"C:\temp\file.txt",
    @"temp\file.txt",
    "file://C:/temp/file.txt:some.exe",
    @"\\shared\users\temp\file.txt.gz",
    "/usr/lib/temp/file.txt"
]
| extend path_parts = parse_path(p)

```

|p|path_parts
|---|---
|C:\temp\file.txt|{"Scheme":"","루트 패스":"C:","디렉터리 패스":"C:\\temp","디렉터리 이름":"temp","파일 이름":"file.txt","확장":"txt","AlternateDataStreamName":""}
|temp\file.txt|{"Scheme":"","루트 패스":"","디렉터리 패스":"temp","디렉터리 이름":"temp","파일 이름":"file.txt","확장":"txt","AlternateDataStreamName":""}
|file://C:/temp/file.txt:some.exe|{"Scheme":"파일","루트 패스":"C:","디렉터리 패스":"C:/temp","디렉터리 이름":"temp","파일 이름":"file.txt","확장/txt","AlternateDataStreamName":"some.exe"}
|\\공유\사용자\temp\file.txt.gz|{"Scheme":"","RootPath":","디렉터리 패스":"","디렉터리 패스":"공유\\\\\\사용자\\temp","디렉터리 이름":"temp","파일 이름":"file.txt.gz","확장":"gz","AlternateDataStreamName":""}
|/usr/lib/temp/file.txt|{"Scheme":""루트 패스":","","디렉터리 패스":"/usr/lib/temp","디렉터리이름":"temp","파일 이름":"file.txt","확장/txt","AlternateDataStreamName":""}
