---
title: parse_path ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_path ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 07224c01e1bd226575aff6555ca7cd0a82c049b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251670"
---
# <a name="parse_path"></a>parse_path()

파일 경로를 구문 분석 `string` 하 고 [`dynamic`](./scalar-data-types/dynamic.md) 경로의 다음 부분을 포함 하는 개체를 반환 합니다.
* 구성표
* RootPath
* DirectoryPath
* DirectoryName
* FileName
* 확장명
* AlternateDataStreamName

함수는 두 가지 형식의 슬래시가 있는 간단한 경로 외에도 다음과 같은 경로를 지원 합니다.
* 스키마. 예: "file://"
* 공유 경로. 예: " \\ shareddrive\users..."
* 긴 경로. 예 \\ : "? \c: ..." "
* 대체 데이터 스트림 예: "file1.exe:file2.exe"

## <a name="syntax"></a>구문

`parse_path(`*path*`)`

## <a name="arguments"></a>인수

* *경로*: 파일 경로를 나타내는 문자열입니다.

## <a name="returns"></a>반환

`dynamic`위에 나열 된 경로 구성 요소를 포함 하는 형식의 개체입니다.

## <a name="example"></a>예제

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
|C:\temp\file.txt|{"Scheme": "", "RootPath": "C:", "DirectoryPath": "C: \\ temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": ""}
|temp\file.txt|{"Scheme": "", "RootPath": "", "DirectoryPath": "temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": ""}
|file://C:/temp/file.txt:some.exe|{"Scheme": "file", "RootPath": "C:", "DirectoryPath": "C:/temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": "some.exe"}
|\\shared\users\temp\file.txt release.tar.gz|{"Scheme": "", "RootPath": "", "DirectoryPath": " \\ \\ 공유 \\ 사용자 \\ 임시 "," DirectoryName ":" temp "," Filename ":" file.txt. release.tar.gz "," Extension ":" release.tar.gz "," AlternateDataStreamName ":" "}
|/usr/lib/temp/file.txt|{"Scheme": "", "RootPath": "", "DirectoryPath": "/usr/lib/temp", "DirectoryName": "temp", "Filename": "file.txt", "Extension": "txt", "AlternateDataStreamName": ""}
