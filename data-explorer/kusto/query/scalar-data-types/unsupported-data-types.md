---
title: 지원되지 않는 스칼라 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 지원되지 않는 스칼라 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 76a548abdf05cec57e4d0558e5d98ab0f7cbdc3e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509591"
---
# <a name="unsupported-scalar-data-types"></a>지원되지 않는 스칼라 데이터 유형

문서화되지 않은 모든 **스칼라 데이터 형식은** Kusto에서 지원되지 않는 것으로 간주됩니다.

지원되지 않는 형식 중에는 다음과 같은 형식이 있습니다. 일부 형식은 이전에 지원되었습니다.

| Type       | 추가 이름   | 해당하는.NET 형식              | 스토리지 유형(내부 이름)|
| ---------- | -------------------- | --------------------------------- | ----------------------------|
| `float`    |                      | `System.Single`                   | `R32`                       |
| `int16`    |                      | `System.Int16`                    | `I16`                       |
| `uint16`   |                      | `System.UInt16`                   | `UI16`                      |
| `uint32`   | `uint`               | `System.UInt32`                   | `UI32`                      |
| `uint64`   | `ulong`              | `System.UInt64`                   | `UI64`                      |
| `uint8`    | `byte`               | `System.Byte`                     | `UI8`                       |