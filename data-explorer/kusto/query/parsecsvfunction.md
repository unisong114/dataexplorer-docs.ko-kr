---
title: parse_csv() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_csv()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6645fd0dcc7062a4afb60fb34ade1c519af27294
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663683"
---
# <a name="parse_csv"></a>parse_csv()

쉼표 분리 된 값의 단일 레코드를 나타내는 주어진 된 문자열을 분할 하 고 이러한 값으로 문자열 배열을 반환 합니다.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**구문**

`parse_csv(`*소스*`)`

**인수**

* *소스*: 쉼표 분리 된 값의 단일 레코드를 나타내는 소스 문자열입니다.

**반환**

분할 값을 포함하는 문자열 배열입니다.

**참고 사항**

포함된 줄 피드, 쉼표 및 따옴표는 이중 따옴표(''')를 사용하여 이스케이프될 수 있습니다. 이 함수는 행당 여러 레코드를 지원하지 않습니다(첫 번째 레코드만 수행).

**예**

```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "아아",<br>  "b, b, b",<br>  "cc",<br>  "따옴표 \"\"를 이스케이프 : 제목 ",<br>  "라인1\nline2"<br>]|

여러 레코드가 있는 CSV 페이로드:

```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "레코드1",<br>  "a",<br>  "b",<br>  "c"<br>]|