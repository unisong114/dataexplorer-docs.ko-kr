---
title: parse_path ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_path ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 16b80c86f526cb05514577359603e9e21de80064
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224887"
---
# <a name="parse_path"></a>parse_path()

파일 경로를 구문 분석 `string` 하 고 [`dynamic`](./scalar-data-types/dynamic.md) path의 다음 부분을 포함 하는 개체를 반환 합니다. Scheme, rootpath, DirectoryPath, DirectoryName, FileName, Extension, AlternateDataStreamName.
에서는 두 가지 유형의 단순 경로 외에도 스키마 (예: "file://"), 공유 경로 (예: " \\ shareddrive\users..."), 긴 경로 (예 \\ : "? \c: ..." "), 대체 데이터 스트림 (예:" file1: file2 .exe ")의 경로를 지원 합니다.

**구문**

`parse_path(`*path*`)`

**인수**

* *경로*: 파일 경로를 나타내는 문자열입니다.

**반환**

`dynamic`위에 나열 된 경로 구성 요소를 포함 하는 형식의 개체입니다.

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
|C:\temp\file.txt|{"Scheme": "", "RootPath": "C:", "DirectoryPath": "C: \\ temp", "DirectoryName": "temp", "Filename": ".txt", "Extension": "txt", "AlternateDataStreamName": ""}
|temp\file.txt|{"Scheme": "", "RootPath": "", "DirectoryPath": "temp", "DirectoryName": "temp", "Filename": "", "Extension": "txt", "AlternateDataStreamName": ""}
|file://C:/temp/file.txt:some.exe|{"Scheme": "file", "RootPath": "C:", "DirectoryPath": "C:/temp", "DirectoryName": "temp", "Filename": "", "Extension": "txt", "AlternateDataStreamName": ""}
|\\shared\users\temp\file.txt.gz|{"Scheme": "", "RootPath": "", "DirectoryPath": " \\ \\ 공유 \\ 사용자 \\ 임시 "," DirectoryName ":" temp "," Filename ":" release.tar.gz "," Extension ":" release.tar.gz "," AlternateDataStreamName ":" "}
|/usr/lib/temp/file.txt|{"Scheme": "", "RootPath": "", "DirectoryPath": "/usr/lib/temp", "DirectoryName": "temp", "Filename": "", "Extension": "txt", "AlternateDataStreamName": ""}
