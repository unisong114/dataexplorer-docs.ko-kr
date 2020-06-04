---
title: ipv4_compare ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 ipv4_compare ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 67887aac4ab04e016ed63045e66ebcfab343c135
ms.sourcegitcommit: 8953d09101f4358355df60ab09e55e71bc255ead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420869"
---
# <a name="ipv4_compare"></a>ipv4_compare()

두 IPv4 문자열을 비교 합니다. 인수 접두사에서 계산 된 결합 된 IP 접두사 마스크와 선택적 인수를 고려 하는 동안 두 IPv4 문자열이 구문 분석 되 고 비교 됩니다 `PrefixMask` .

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**구문**

`ipv4_compare(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

**인수**

* *Expr1*, *Expr2*: IPv4 주소를 나타내는 문자열 식입니다. [IP 접두사 표기법](#ip-prefix-notation)을 사용 하 여 IPv4 문자열을 마스킹할 수 있습니다.
* *PrefixMask*: 고려 되는 가장 중요 한 비트 수를 나타내는 0에서 32 사이의 정수입니다.

## <a name="ip-prefix-notation"></a>IP 접두사 표기법
 
IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` .
슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 32)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다. 

**예:** 192.168.2.0/24에는 24 개의 연속 비트를 포함 하는 네트워크/subnetmask와 점으로 구분 된 10 진수 형식의 255.255.255.0이 포함 됩니다.

**반환**

* `0`: 첫 번째 IPv4 문자열 인수의 긴 표현이 두 번째 IPv4 문자열 인수와 동일한 경우
* `1`: 첫 번째 IPv4 문자열 인수의 긴 표현이 두 번째 IPv4 문자열 인수 보다 큰 경우
* `-1`: 첫 번째 IPv4 문자열 인수의 긴 표현이 두 번째 IPv4 문자열 인수 보다 작은 경우
* `null`: 두 IPv4 문자열 중 하나에 대 한 변환이 실패 한 경우

## <a name="examples-ipv4-comparison-equality-cases"></a>예: IPv4 비교 같음 사례

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv4-strings"></a>IPv4 문자열 내에 지정 된 IP 접두사 표기법을 사용 하 여 ip 비교

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv4-strings-and-as-additional-argument-of-the-ipv4_compare-function"></a>IPv4 문자열 내에 지정 된 IP 접두사 표기법을 사용 하 여 IP를 비교 하 고 함수에 대 한 추가 인수를 사용 합니다. `ipv4_compare()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|접두사|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|

