---
title: ipv6_is_match ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 ipv6_is_match () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: d5bd270e016f1694c28f663a7fe8bf1d9a8f0903
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301285"
---
# <a name="ipv6_is_match"></a>ipv6_is_match ()

두 IPv6 또는 IPv4 네트워크 주소 문자열과 일치 합니다. 인수 접두사에서 계산 된 결합 된 IP 접두사 마스크와 선택적 인수를 고려 하 여 두 개의 IPv6/IPv4 문자열이 구문 분석 되 고 비교 됩니다 `PrefixMask` .

```kusto
ipv6_is_match('::ffff:7f00:1', '127.0.0.1') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995') == false
ipv6_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv6_is_match('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == true
```

**구문**

`ipv6_is_match(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

**인수**

* *Expr1*, *Expr2*: IPv6 또는 IPv4 주소를 나타내는 문자열 식입니다. [IP 접두사 표기법](#ip-prefix-notation)을 사용 하 여 IPv6 및 IPv4 문자열을 마스킹할 수 있습니다.
* *PrefixMask*: 고려 되는 가장 중요 한 비트 수를 나타내는 0에서 128 사이의 정수입니다.

## <a name="ip-prefix-notation"></a>IP 접두사 표기법
 
IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` .
슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 127)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다. 

**예**: fe80:: 85d: e82c: 9446:7994/120에는 120 연속 비트를 포함 하는 연결 된 net/subnetmask가 있습니다.

**반환**

* `true`: 첫 번째 IPv6/IPv4 문자열 인수의 긴 표현이 두 번째 IPv6/IPv4 문자열 인수와 동일한 경우
* `false`그렇지.
* `null`: 두 IPv6/IPv4 문자열 중 하나에 대 한 변환이 실패 한 경우

> [!Note]
> 함수는 IPv6 및 IPv4 네트워크 주소를 모두 나타내는 인수를 받아들이고 비교할 수 있습니다. 호출자가 해당 인수가 IPv4 형식이 면 [ipv4_is_match ()](./ipv4-is-matchfunction.md) 함수를 사용 합니다. 이 함수를 실행 하면 런타임 성능이 향상 됩니다.

## <a name="examples"></a>예

### <a name="ipv6ipv4-comparison-equality-case---ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>I p v 6/IPv4 비교 같음 대/소문자-IP 접두사 표기법이 i p v 6/IPv4 문자열 내에 지정 되었습니다.

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
| extend result = ipv6_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|
|fe80:: 85d: e82c: 9446:7994|fe80:: 85d: e82c: 9446:7994|1|
|fe80:: 85d: e82c: 9446:7994/120|fe80:: 85d: e82c: 9446:7998|1|
|fe80:: 85d: e82c: 9446:7994|fe80:: 85d: e82c: 9446:7998/120|1|
|fe80:: 85d: e82c: 9446:7994/120|fe80:: 85d: e82c: 9446:7998/120|1|
|192.168.1.1|:: ffff: c0a8:0101|1|
|192.168.1.1/24|:: ffff: c0a8:01ff|1|
|:: ffff: c0a8:0101|192.168.1.255/24|1|
|:: 192.168.1.1/30|192.168.1.255/24|1|


### <a name="ipv6ipv4-comparison-equality-case--ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_is_match-function"></a>IPv6/IPv4 비교 같음 대/소문자 구분-IP 접두사 표기법은 IPv6/IPv4 문자열 내에 지정 하 고 함수의 추가 인수로 지정 합니다. `ipv6_is_match()`

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
| extend result = ipv6_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|접두사|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
|fe80:: 85d: e82c: 9446:7994|fe80:: 85d: e82c: 9446:7995|127|1|
|fe80:: 85d: e82c: 9446:7994/127|fe80:: 85d: e82c: 9446:7998|120|1|
|fe80:: 85d: e82c: 9446:7994/120|fe80:: 85d: e82c: 9446:7998|127|1|
|192.168.1.1/24|:: ffff: c0a8:01ff|127|1|
|:: ffff: c0a8:0101|192.168.1.255|120|1|
|:: 192.168.1.1/30|192.168.1.255/24|127|1|
