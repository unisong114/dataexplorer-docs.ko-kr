---
title: ipv4_is_match() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 ipv4_is_match()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: aa0646321af2d467c1e4af07fba81ccdc58eff37
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513739"
---
# <a name="ipv4_is_match"></a>ipv4_is_match()

두 개의 IPv4 문자열을 일치시다.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**구문**

`ipv4_is_match(`*Expr1*`, `*Expr2*`[ ,`*접두사 마스크*`])`

**인수**

* *Expr1*, *Expr2*: IPv4 주소를 나타내는 문자열 표현식입니다. IPv4 문자열은 [IP 접두사 표기술을](#ip-prefix-notation)사용하여 마스커링할 수 있습니다.
* *접두사 마스크*: 0에서 32까지의 정수는 고려되는 가장 중요한 비트수를 나타냅니다.

### <a name="ip-prefix-notation"></a>IP 접두사 표기

슬래시 () `IP-prefix notation` `/`문자를 사용하여 IP 주소를 정의하는 것이 일반적입니다. 슬래시의 왼쪽에 있는 IP`/`주소() 는 기본 IP 주소이고 슬래시()의`/`오른쪽에 있는 숫자(1~32)는 넷마스크의 연속 1비트수입니다. 

예: 192.168.2.0/24에는 연속 비트 24개 또는 점선 소수점 255.255.0을 포함하는 연결된 순/서브넷마스크가 있습니다.

**반환**

인수 접두사및 선택적 `PrefixMask` 인수에서 계산된 결합된 IP 접두사 마스크를 고려하면서 두 개의 IPv4 문자열은 구문 분석및 비교됩니다.

HRESULT = NO_ERROR를
* `true`: 첫 번째 IPv4 문자열 인수의 긴 표현이 두 번째 IPv4 문자열 인수와 동일한 경우.
*  `false`: 그렇지 않은 경우.

두 IPv4 문자열 중 하나에 대한 변환이 성공적이지 `null`않으면 결과가 됩니다.

**예**

## <a name="ipv4-comparison-equality-cases"></a>IPv4 비교 평등 사례

다음 예제는 IPv4 문자열 내에 지정된 IP 접두사 표기와 함께 다양한 IP를 비교합니다.

```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

다음 예제는 IPv4 문자열 내에 지정된 IP 접두사 표기법과 `ipv4_is_match()` 함수의 추가 인수로 다양한 IP를 비교합니다.

```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|접두사|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|