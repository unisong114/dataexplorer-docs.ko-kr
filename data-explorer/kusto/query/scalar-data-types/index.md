---
title: 스칼라 데이터 형식 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 스칼라 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a7508866f85bb7edb5a6feee5cfe9d191b946a09
ms.sourcegitcommit: c09cc374d5d1d8b396c466ef397690b4b7e4174f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103481547"
---
# <a name="scalar-data-types"></a>스칼라 데이터 형식

모든 데이터 값(예: 식의 값 또는 함수에 대한 매개 변수)에는 **데이터 형식** 이 있습니다. 데이터 형식은 **스칼라 데이터 형식**(아래에 나열된 기본 제공 미리 정의된 형식 중 하나) 또는 **사용자 정의 레코드**(테이블 행의 데이터 형식과 같이 순서가 지정된 이름/스칼라 데이터 형식 쌍의 시퀀스)입니다.

Kusto는 Kusto에서 사용할 수 있는 모든 형식의 데이터를 정의하는 시스템 데이터 형식 세트를 제공합니다.

> [!NOTE]
> 사용자 정의 데이터 형식은 Kusto에서 지원하지 않습니다.

다음 표에는 Kusto가 지원하는 데이터 형식과 이를 참조하는 데 사용할 수 있는 추가 별칭 및 거의 동등한 .NET Framework 형식이 나와 있습니다.

| Type       | 추가 이름   | 해당하는.NET 형식              | gettype()   |
| ---------- | -------------------- | --------------------------------- | ----------- |
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |
| `dynamic`  |                      | `System.Object`                   | `array`, `dictionary` 또는 다른 값 중 하나 |
| `guid`     |                      | `System.Guid`                     | `guid`      |
| `int`      |                      | `System.Int32`                    | `int`       |
| `long`     |                      | `System.Int64`                    | `long`      |
| `real`     | `double`             | `System.Double`                   | `real`      |
| `string`   |                      | `System.String`                   | `string`    |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   |

문자열이 아닌 모든 데이터 형식에는 데이터 부족 또는 데이터 불일치를 나타내는 특별한 "null" 값이 포함됩니다. 예를 들어 `"abc"` 문자열을 `int` 열에 수집하려고 하면 이 값이 반환됩니다.
이 값을 명시적으로 구체화할 수는 없지만, `isnull()` 함수를 사용하여 식이 이 값으로 평가되는지 여부를 검색할 수 있습니다.

> [!WARNING]
> `guid` 형식에 대한 지원이 완전하지 않습니다.
> 대신 팀에서 `string` 형식의 값을 사용하는 것이 좋습니다.
