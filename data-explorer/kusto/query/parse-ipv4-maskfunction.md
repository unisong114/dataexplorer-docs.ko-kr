---
title: parse_ipv4_mask() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_ipv4_mask()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 994c1e551d7c38bb1493579bcf10438acd2923f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511784"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

IPv4 및 netmask의 입력 문자열을 긴 숫자 표현(서명된 64비트)으로 변환합니다.

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432

parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31) 
```

**구문**

`parse_ipv4_mask(`*익스프레서*`, `*접두사 마스크*`)`

**인수**

* *예절*: 길게 변환될 IPv4 주소의 문자열 표현입니다. 
* *접두사 마스크*: 0에서 32까지의 정수는 고려되는 가장 중요한 비트수를 나타냅니다.

**반환**

변환에 성공하면 결과는 긴 수입니다.
변환에 성공하지 못하면 결과가 됩니다. `null`