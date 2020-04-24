---
title: 정규식 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 정규식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0bc5dc6705d6cada446716f2f9d84618322ecd76
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510526"
---
# <a name="regular-expressions"></a>정규식

RE2 정규식 구문은 Kusto(re2)에서 사용하는 정규식 라이브러리의 구문을 설명합니다.
Kusto에는 정규식을 사용하여 문자열 일치, 선택 및 추출을 수행하는 몇 가지 함수가 있습니다.

- [countof()](countoffunction.md)
- [추출()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse 연산자](parseoperator.md)
- [바꾸기()](replacefunction.md)
- [트림()](trimfunction.md)
- [트리엔드()](trimendfunction.md)
- [트림 스타트()](trimstartfunction.md)

Kusto에서 지원하는 정규식 구문은 [re2 라이브러리의](https://github.com/google/re2/wiki/Syntax)구문이며 아래에 자세히 설명되어 있습니다. 이러한 식은 Kusto에서 리터럴로 `string` 인코딩되어야 하며 Kusto의 모든 문자열 인용 규칙이 적용됩니다. 예를 들어 정규식은 `\A` 줄의 시작부분과 일치하며(아래 표 참조), Kusto에서 `"\\A"` 문자열 리터럴로 지정됩니다("추가" 백슬래시 ()`\`문자 참고).

