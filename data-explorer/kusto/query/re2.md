---
title: 정규식 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 정규식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513083"
---
# <a name="re2-syntax"></a>RE2 구문

RE2 정규식 구문은 Kusto(re2)에서 사용하는 정규식 라이브러리의 구문을 설명합니다.
정규식을 사용하여 문자열 일치, 선택 및 추출을 수행하는 Kusto에는 몇 가지 함수가 있습니다.

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse 연산자](parseoperator.md)
- [replace()](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

Kusto에서 지원하는 정규식 구문은 [re2 라이브러리](https://github.com/google/re2/wiki/Syntax)의 구문입니다. 이러한 식은 Kusto에서 `string` 리터럴로 인코딩되어야 하며 Kusto의 모든 문자열 따옴표 규칙이 적용됩니다. 예를 들어 정규식 `\A`는 줄의 시작 부분과 일치하며, Kusto에서 문자열 리터럴 `"\\A"`로 지정됩니다("추가" 백슬래시(`\`) 문자 참고).
