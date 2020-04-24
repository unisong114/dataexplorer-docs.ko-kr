---
title: parse_user_agent() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_user_agent()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ebe85c622dda116366e30ba22e2e495e4cb76ac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511733"
---
# <a name="parse_user_agent"></a>parse_user_agent()

사용자의 브라우저를 식별하고 사용자가 방문하는 웹 사이트를 호스팅하는 서버에 특정 시스템 세부 정보를 제공하는 사용자 에이전트 문자열을 해석합니다. 결과가 로 [`dynamic`](./scalar-data-types/dynamic.md)반환됩니다. 

**구문**

`parse_user_agent(`*사용자 에이전트 문자열,* *찾기*`)`

**인수**

* *사용자 에이전트 문자열*: 사용자 `string`에이전트 문자열을 나타내는 형식의 식입니다.

* *look-for*: 사용자 `string` 에이전트 `dynamic`문자열(대상 구문 분석)에서 함수가 무엇을 찾아야 하는지 나타내는 형식 또는 의 식입니다. 가능한 옵션 : "브라우저", "OS", "장치". 단일 구문 분석 대상만 필요한 경우 매개 `string` 변수를 전달할 수 있습니다.
두 개 또는 세 개가 필요한 `dynamic array`경우 을 로 전달할 수 있습니다.

**반환**

요청된 구문 분석 대상에 대한 정보를 포함하는 형식의 `dynamic` 개체입니다.

브라우저 : 가족, 메이저 버전, 마이너 버전, 패치                 

운영 체제: 가족, 메이저버전, 마이너버전, 패치, 패치마이너             

장치: 가족, 상표, 모델

> [!WARNING]
> 함수 구현은 입력 문자열의 정규식 검사를 기반으로 미리 정의된 수많은 패턴에 대해 빌드됩니다. 따라서 예상 된 시간과 CPU 소비가 높습니다.
함수가 쿼리에서 사용되는 경우 여러 컴퓨터에서 분산된 방식으로 실행되는지 확인합니다.
이 함수를 사용하는 쿼리가 자주 사용되는 경우 [업데이트 정책을](../management/updatepolicy.md)통해 결과를 미리 만들 수 있지만 업데이트 정책 내에서 이 함수를 사용하면 인서션 대기 시간이 늘어나갑니다.
 
**예제**

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

예상 결과는 동적 개체입니다.

{ "브라우저": {"가족": "어도비에어", "메이저버전": "2", "마이너버전": "5", "패치": "1" }

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

예상 결과는 동적 개체입니다.

{ "브라우저": { "가족": "노키아 OSS 브라우저", "메이저 버전": "3", "마이너 버전": "1", "패치": "1", "운영 체제": {"가족": "심비안 OS", "메이저 버전": "9", "마이너 버전": "2", "패치": """"패치마이너": """""" }, "장치": {"가족": "노키아 N81", "노키아 N81", "브랜드"