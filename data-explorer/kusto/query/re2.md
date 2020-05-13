---
title: 정규식-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 정규식에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 3bbd14031adbfee3b5fac07194f5ff879ff33693
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373066"
---
# <a name="regular-expressions"></a>정규식

RE2 regular expression 구문은 Kusto (RE2)에서 사용 하는 정규식 라이브러리의 구문을 설명 합니다.
정규식을 사용 하 여 문자열 일치, 선택 및 추출을 수행 하는 Kusto에는 몇 가지 함수가 있습니다.

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse 연산자](parseoperator.md)
- [replace ()](replacefunction.md)
- [trim ()](trimfunction.md)
- [trimend ()](trimendfunction.md)
- [trimstart ()](trimstartfunction.md)

Kusto에서 지원 되는 정규식 구문은 [re2 라이브러리](https://github.com/google/re2/wiki/Syntax)의 식입니다. 이러한 식은 Kusto로 인코딩해야 하며 `string` 모든 kusto의 문자열 따옴표 규칙이 적용 됩니다. 예를 들어, 정규식은 `\A` 줄의 시작 부분을 찾지만 Kusto에 문자열 리터럴로 지정 됩니다 `"\\A"` ("추가" 백슬래시 ( `\` ) 문자 참조).
