---
title: parse_command_line ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_command_line ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: 0296b41dc10092f0b274491c3fab3355fc82a2d9
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2020
ms.locfileid: "85518143"
---
# <a name="parse_command_line"></a>parse_command_line ()

유니코드 명령줄 문자열을 구문 분석 하 고 명령줄 인수의 동적 배열을 반환 합니다.

**구문**

`parse_command_line(`*command_line*,*parser_type*`)`

**인수**

* *command_line*: 구문 분석할 명령줄입니다.
* *parser_type*: 현재 지원 되는 유일한 값은 `"Windows"` [CommandLineToArgvW](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)와 동일한 방식으로 명령줄을 구문 분석 하는입니다.

**반환**

명령줄 인수의 동적 배열입니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|결과|
|---|
|["echo", "hello 세계!"]|
