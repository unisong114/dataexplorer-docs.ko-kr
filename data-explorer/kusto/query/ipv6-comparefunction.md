---
title: ipv6_compare ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 ipv6_compare () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 7d63ce48ba54377fa79ccd13484b2b9b08794bc6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512370"
---
# <a name="ipv6_compare"></a>ipv6_compare()

두 IPv6 또는 IPv4 네트워크 주소 문자열을 비교 합니다. 인수 접두사에서 계산 된 결합 된 IP 접두사 마스크와 선택적 인수를 고려 하는 동안 두 IPv6 문자열이 구문 분석 되 고 비교 됩니다 `PrefixMask` .

```kusto
ipv6_compare('::ffff:7f00:1', '127.0.0.1') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995')  < 0
ipv6_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv6_compare('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == 0
```

**구문**

`ipv6_compare(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

**인수**

* *Expr1*, *Expr2*: IPv6 또는 IPv4 주소를 나타내는 문자열 식입니다. IP 접두사 표기법을 사용 하 여 IPv6 및 IPv4 문자열을 마스킹할 수 있습니다 (참고 참조).
* *PrefixMask*: 0에서 128 사이의 정수로, 고려 되는 가장 중요 한 비트 수를 나타냅니다.

> [!Note] 
>**IP 접두사 표기법**
> 
>일반적으로 `IP-prefix notation` 슬래시 () 문자를 사용 하 여 IP 주소를 정의 하는 것이 좋습니다 `/` .
>슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소이 고, 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 127)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다. 
>
> **예**: fe80:: 85d: e82c: 9446:7994/120에는 120 연속 비트를 포함 하는 연결 된 net/subnetmask가 있습니다.

**반환**

* `0`: 첫 번째 IPv6 문자열 인수의 긴 표현이 두 번째 IPv6 문자열 인수와 동일한 경우
* `1`: 첫 번째 IPv6 문자열 인수의 긴 표현이 두 번째 IPv6 문자열 인수 보다 큰 경우
* `-1`: 첫 번째 IPv6 문자열 인수의 긴 표현이 두 번째 IPv6 문자열 인수 보다 작은 경우
* `null`: 두 IPv6 문자열 중 하나에 대 한 변환이 실패 한 경우

> [!Note]
> 함수는 IPv6 및 IPv4 네트워크 주소를 모두 나타내는 인수를 받아들이고 비교할 수 있습니다. 그러나 호출자가 인수를 IPv4 형식으로 알고 있으면 [ipv4_is_compare ()](./ipv4-comparefunction.md) 함수를 사용 합니다. 이 함수를 실행 하면 런타임 성능이 향상 됩니다.

## <a name="examples-ipv6ipv4-comparison-equality-cases"></a>예: IPv6/IPv4 비교 같음 사례

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>I p v 6/IPv4 문자열 내에 지정 된 IP 접두사 표기법을 사용 하 여 ip 비교

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 // IPv4 are compared as IPv6 addresses
 '192.168.1.1',    '192.168.1.1',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
  // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7994',         // Equal IPs
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7998/120',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998/120', // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1',      '::ffff:c0a8:0101', // Equal IPs
 '192.168.1.1/24',   '::ffff:c0a8:01ff', // 24 bit IP-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|
|fe80:: 85d: e82c: 9446:7994|fe80:: 85d: e82c: 9446:7994|0|
|fe80:: 85d: e82c: 9446:7994/120|fe80:: 85d: e82c: 9446:7998|0|
|fe80:: 85d: e82c: 9446:7994|fe80:: 85d: e82c: 9446:7998/120|0|
|fe80:: 85d: e82c: 9446:7994/120|fe80:: 85d: e82c: 9446:7998/120|0|
|192.168.1.1|:: ffff: c0a8:0101|0|
|192.168.1.1/24|:: ffff: c0a8:01ff|0|
|:: ffff: c0a8:0101|192.168.1.255/24|0|
|:: 192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_compare-function"></a>I p v 6/IPv4 문자열 내에 지정 된 IP 접두사 표기법과 함수의 추가 인수를 사용 하 여 ip를 비교 합니다. `ipv6_compare()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 // IPv4 are compared as IPv6 addresses 
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP4-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP4-prefix is used for comparison
   // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995',     127, // 127 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7998', 120, // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998', 127, // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1/24',   '::ffff:c0a8:01ff', 127, // 127 bit IP6-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255',    120, // 120 bit IP6-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', 127, // 120 bit IP6-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|접두사|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|
|fe80:: 85d: e82c: 9446:7994|fe80:: 85d: e82c: 9446:7995|127|0|
|fe80:: 85d: e82c: 9446:7994/127|fe80:: 85d: e82c: 9446:7998|120|0|
|fe80:: 85d: e82c: 9446:7994/120|fe80:: 85d: e82c: 9446:7998|127|0|
|192.168.1.1/24|:: ffff: c0a8:01ff|127|0|
|:: ffff: c0a8:0101|192.168.1.255|120|0|
|:: 192.168.1.1/30|192.168.1.255/24|127|0|

