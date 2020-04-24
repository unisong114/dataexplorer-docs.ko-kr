---
title: translate() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 translate()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: f128ce31af7fc720ee81b7471d3d74616197b8d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505715"
---
# <a name="translate"></a>translate()

지정된 문자열에서 문자 집합('searchList')을 다른 문자 집합('replacementList')으로 바꿉니다.
함수는 'searchList'에서 문자를 검색하고 'replacementList'의 해당 문자로 대체합니다.

**구문**

`translate(`*검색목록* `,` *교체목록* `,` *텍스트*`)`

**인수**

* *searchList*: 대체해야 하는 문자 목록
* *대체 목록*: 'searchList'의 문자를 대체해야 하는 문자 목록
* *텍스트*: 검색할 문자열

**반환**

'replacementList'에 있는 모든 문자를 'searchList'의 해당 문자로 대체한 후 *텍스트*

**예**

|입력                                 |출력   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|