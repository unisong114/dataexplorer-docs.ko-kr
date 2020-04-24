---
title: bool 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 bool 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: dd9cfa4f97f3baa4353f967f5e1ca31186f4815f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509931"
---
# <a name="the-bool-data-type"></a>bool 데이터 형식

`bool` ()`boolean`데이터 형식은 null 값뿐만 `true` `false` 아니라 두 `1` `0`가지 상태 중 하나를 가질 수 있습니다.

## <a name="bool-literals"></a>불 리터럴

데이터 `bool` 형식에는 다음과 같은 리터럴이 있습니다.
* `true`그리고 `bool(true)`: 참름을 표현
* `false`그리고 `bool(false)`: 거짓을 나타내는
* `bool(null)`: [null 값](null-values.md) 참조

## <a name="bool-operators"></a>bool 연산자

데이터 `bool` 형식은 같음 (),`==`같음`!=`(), 부등식 ( ), 논리 및 ()`and`및 논리 -or ()를`or`지원합니다.