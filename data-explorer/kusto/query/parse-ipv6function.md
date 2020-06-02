---
title: parse_ipv6 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 parse_ipv6 () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: d7858a67719bbde9a1ecedee5888abc7d0662e1e
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301217"
---
# <a name="parse_ipv6"></a>parse_ipv6 ()

IPv6 또는 IPv4 문자열을 정식 IPv6 문자열 표현으로 변환 합니다.

```kusto
parse_ipv6("127.0.0.1") == '0000:0000:0000:0000:0000:ffff:7f00:0001'
parse_ipv6(":fe80::85d:e82c:9446:7994") == 'fe80:0000:0000:0000:085d:e82c:9446:7994'
```

**구문**

`parse_ipv6(`*`Expr`*`)`

**인수**

* *`Expr`*: 정식 IPv6 표현으로 변환 될 IPv6/IPv4 네트워크 주소를 나타내는 문자열 식입니다. 문자열에는 [IP 접두사 표기법](#ip-prefix-notation)을 사용한 네트 마스크가 포함 될 수 있습니다.

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
 '192.168.255.255',     32,  // 32-bit netmask is used
 '192.168.255.255/24',  30,  // 24-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255',     24,  // 24-bit netmask is used
]
| extend ip_long = parse_ipv4_mask(ip_string, netmask)
```

|ip_string|네트워크|ip_long|
|---|---|---|
|192.168.255.255|32|3232301055|
|192.168.255.255/24|30|3232300800|
|255.255.255.255|24|4294967040|

## <a name="next-steps"></a>다음 단계

다른 유사한 함수는 다음을 참조 하세요.

* [parse_ipv4()](parse-ipv4function.md)
* [ipv6_compare ()](ipv6-comparefunction.md)
* [ipv6_is_match ()](ipv6-is-matchfunction.md)
