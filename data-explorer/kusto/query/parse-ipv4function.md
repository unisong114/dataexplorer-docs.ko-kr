---
title: parse_ipv4() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_ipv4()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 296c7e77a2397501ae086e16154ca249249c23e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511750"
---
# <a name="parse_ipv4"></a>parse_ipv4()

IPv4 문자열을 긴(서명된 64비트) 숫자 표현으로 변환합니다.

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**구문**

`parse_ipv4(`*Expr*`)`

**인수**

* *예절*: 길게 변환되는 IPv4를 나타내는 문자열 표현식입니다. 문자열에는 [IP 접두사 표기술을](#ip-prefix-notation)사용하는 네트 마스크가 포함될 수 있습니다.

### <a name="ip-prefix-notation"></a>IP 접두사 표기

슬래시 () `IP-prefix notation` `/`문자를 사용하여 IP 주소를 정의하는 것이 일반적입니다.
슬래시의 왼쪽에 있는 IP`/`주소() 는 기본 IP 주소이고 슬래시(/)의 오른쪽에 있는 숫자(1~32)는 넷마스크의 연속 1비트 수입니다. 따라서 192.168.2.0/24에는 연속 비트 24개 또는 점선 소수점 형식의 255.255.255.0이 포함된 연결된 순/서브넷마스크가 있습니다.

**반환**

변환에 성공하면 결과는 긴 숫자가 됩니다.
변환에 성공하지 못하면 결과가 `null`됩니다.
 
**예제**

```kusto
datatable(ip_string:string)
[
 '192.168.1.1',
 '192.168.1.1/24',
 '255.255.255.255/31'
]
| extend ip_long = parse_ipv4(ip_string)
```

|ip_string|ip_long|
|---|---|
|192.168.1.1|3232235777|
|192.168.1.1/24|3232235776|
|255.255.255.255/31|4294967294|