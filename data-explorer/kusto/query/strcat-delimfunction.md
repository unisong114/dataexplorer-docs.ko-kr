---
title: strcat_delim() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 strcat_delim()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f944af741cd5f655c2c9b090ddebc6cc35a47766
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506939"
---
# <a name="strcat_delim"></a>strcat_delim()

첫 번째 인수로 제공되는 구분 기호와 함께 2개에서 64개 인수 사이에 연결합니다.

 * 인수가 문자열 형식이 아닌 경우 강제로 문자열로 변환됩니다.

**구문**

`strcat_delim(`*구분기호*,*인수1*,*인수2* [, *인수N]*`)`

**인수**

* *구분 기호*: 구분 기호로 사용되는 문자열 표현식입니다.
* *인수1* ... *argumentn* : 통합할 식입니다.

**반환**

인수, *구분 기호가있는*단일 문자열로 연결됩니다.

**예**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|