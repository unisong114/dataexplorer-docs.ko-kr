---
title: 소수점 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 소수점 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62fd745c804ad4a50652e09cd722c17a4a61daac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509897"
---
# <a name="the-decimal-data-type"></a>소수점 데이터 형식

데이터 `decimal` 형식은 128비트 너비의 소수 자릿수를 나타냅니다.

데이터 형식의 `decimal` 리터럴은 와 동일한 표현을 갖습니다. NET의 `System.Data.SqlTypes.SqlDecimal`.

`decimal(1.0)`및 `decimal(0.1)` `decimal(1e5)` 모든 문자 문자 `decimal`문자입니다.

몇 가지 특별한 리터럴 양식이 있습니다.
* `decimal(null)`: null [값입니다.](null-values.md)