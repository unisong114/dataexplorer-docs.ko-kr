---
title: parse_user_agent ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_user_agent ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4b4371ee691cea65096cff683a348fcac31e7e48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346408"
---
# <a name="parse_user_agent"></a>parse_user_agent()

사용자의 브라우저를 식별 하 고 사용자가 방문 하는 웹 사이트를 호스트 하는 서버에 특정 시스템 세부 정보를 제공 하는 사용자 에이전트 문자열을 해석 합니다. 결과는로 반환 됩니다 [`dynamic`](./scalar-data-types/dynamic.md) . 

## <a name="syntax"></a>구문

`parse_user_agent(`*사용자 에이전트-문자열*, *조회*`)`

## <a name="arguments"></a>인수

* *사용자 에이전트 문자열*: `string` 사용자 에이전트 문자열을 나타내는 형식의 식입니다.

* *찾을* `string` `dynamic` 대상: 사용자 에이전트 문자열 (구문 분석 대상)에서 함수를 찾고 있는 항목을 나타내는 또는 형식의 식입니다. 가능한 옵션은 "browser", "os", "device"입니다. 단일 구문 분석 대상만 필요한 경우 매개 변수를 전달할 수 있습니다 `string` .
2 또는 3이 필요한 경우로 전달 될 수 있습니다 `dynamic array` .

## <a name="returns"></a>반환

`dynamic`요청 된 구문 분석 대상에 대 한 정보를 포함 하는 형식의 개체입니다.

브라우저: Family, MajorVersion, MinorVersion, Patch                 

운영 체제: 제품군, MajorVersion, MinorVersion, 패치, PatchMinor             

장치: 제품군, 브랜드, 모델

> [!WARNING]
> 함수 구현은 매우 많은 수의 미리 정의 된 패턴에 대해 입력 문자열의 regex 검사를 기반으로 빌드됩니다. 따라서 예상 시간과 CPU 소비가 높습니다.
쿼리에서 함수를 사용 하는 경우 여러 컴퓨터에서 분산 방식으로 실행 되는지 확인 합니다.
이 함수를 사용 하는 쿼리를 자주 사용 하는 경우 [업데이트 정책을](../management/updatepolicy.md)통해 결과를 미리 만들려고 할 수 있지만 업데이트 정책 내에서이 함수를 사용 하면 수집 대기 시간이 증가 하는 것을 고려해 야 합니다.
 
## <a name="example"></a>예제

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

예상 결과는 동적 개체입니다.

{"Browser": {"Family": "AdobeAIR", "MajorVersion": "2", "MinorVersion": "5", "Patch": "1"}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

예상 결과는 동적 개체입니다.

{"Browser": {"Family": "Nokia OSS Browser", "MajorVersion": "3", "MinorVersion": "1", "Patch": ""}, "운영 체제": {"Family": "Symbian OS", "MajorVersion": "9", "MinorVersion": "2", "Patch": "", "PatchMinor": ""}, "Device": {"Family": "Nokia N81", "Brand": "Nokia", "Model": "N81-3"}}