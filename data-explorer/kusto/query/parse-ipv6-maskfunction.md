---
title: parse_ipv4_mask ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 parse_ipv6_mask () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 99618c4d171e8ccade39ce50b9a0d46f9769f5f8
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301234"
---
# <a name="parse_ipv6_mask"></a>parse_ipv6_mask ()
 
IPv6/IPv4 문자열과 네트워크 마스크를 정식 IPv6 문자열 표현으로 변환 합니다.

```kusto
parse_ipv6_mask("127.0.0.1", 24) == '0000:0000:0000:0000:0000:ffff:7f00:0000'
parse_ipv6_mask(":fe80::85d:e82c:9446:7994", 120) == 'fe80:0000:0000:0000:085d:e82c:9446:7900'
```

**구문**

`parse_ipv6_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**인수**

* *`Expr`*: 정식 IPv6 표현으로 변환 될 IPv6/IPv4 네트워크 주소를 나타내는 문자열 식입니다. 문자열에는 [IP 접두사 표기법](#ip-prefix-notation)을 사용한 네트 마스크가 포함 될 수 있습니다.
* *`PrefixMask`*: 고려 되는 가장 중요 한 비트 수를 나타내는 0에서 128 사이의 정수입니다.

## <a name="ip-prefix-notation"></a>IP 접두사 표기법

IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` .
슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 127)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다.

**반환**

성공적으로 변환 되 면 정식 IPv6 네트워크 주소를 나타내는 문자열이 반환 됩니다.
변환이 성공 하지 못하면 결과는가 됩니다 `null` .

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 // IPv4 addresses
 '192.168.255.255',     120,  // 120-bit netmask is used
 '192.168.255.255/24',  124,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255', 128,  // 128-bit netmask is used
 // IPv6 addresses
 'fe80::85d:e82c:9446:7994', 128,     // 128-bit netmask is used
 'fe80::85d:e82c:9446:7994/120', 124, // 120-bit netmask is used
 // IPv6 with IPv4 notation
 '::192.168.255.255',    128,  // 128-bit netmask is used
 '::192.168.255.255/24', 128,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
]
| extend ip6_canonical = parse_ipv6_mask(ip_string, netmask)
```

|ip_string|네트워크|ip6_canonical|
|---|---|---|
|192.168.255.255|120|0000:0000:0000:0000:0000: ffff: c0a8: ff00|
|192.168.255.255/24|124|0000:0000:0000:0000:0000: ffff: c0a8: ff00|
|255.255.255.255|128|0000:0000:0000:0000:0000: ffff: ffff: ffff|
|fe80:: 85d: e82c: 9446:7994|128|fe80:0000:0000:0000:085d: e82c: 9446:7994|
|fe80:: 85d: e82c: 9446:7994/120|124|fe80:0000:0000:0000:085d: e82c: 9446:7900|
|:: 192.168.255.255|128|0000:0000:0000:0000:0000: ffff: c0a8: ffff|
|:: 192.168.255.255/24|128|0000:0000:0000:0000:0000: ffff: c0a8: ff00|

## <a name="next-steps"></a>다음 단계

다른 유사한 함수는 다음을 참조 하세요.

* [parse_ipv4_mask()](parse-ipv4-maskfunction.md)
* [parse_ipv6 ()](parse-ipv6function.md)
