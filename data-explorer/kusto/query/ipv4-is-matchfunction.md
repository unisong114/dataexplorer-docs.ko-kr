---
title: ipv4_is_match ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 ipv4_is_match ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1abe99a4739d68e6acb76b800af293e77e62b2f0
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803916"
---
# <a name="ipv4_is_match"></a>ipv4_is_match()

두 IPv4 문자열을 찾습니다. 인수 접두사에서 계산 된 결합 된 IP 접두사 마스크와 선택적 인수를 고려 하는 동안 두 IPv4 문자열이 구문 분석 되 고 비교 됩니다 `PrefixMask` .

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

## <a name="syntax"></a>구문

`ipv4_is_match(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

## <a name="arguments"></a>인수

* *Expr1*, *Expr2*: IPv4 주소를 나타내는 문자열 식입니다. [IP 접두사 표기법](#ip-prefix-notation)을 사용 하 여 IPv4 문자열을 마스킹할 수 있습니다.
* *PrefixMask*: 고려 되는 가장 중요 한 비트 수를 나타내는 0에서 32 사이의 정수입니다.

## <a name="ip-prefix-notation"></a>IP 접두사 표기법

IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` . 슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 32)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다. 

예를 들어 192.168.2.0/24에는 24 개의 연속 비트를 포함 하는 연결 된 net/subnetmask와 점으로 구분 된 10 진수 형식의 255.255.255.0이 있습니다.

## <a name="returns"></a>반환

* `true`: 첫 번째 IPv4 문자열 인수의 긴 표현이 두 번째 IPv4 문자열 인수와 동일한 경우
*  `false`그렇지.
* `null`: 두 IPv4 문자열 중 하나에 대 한 변환이 실패 한 경우

## <a name="examples"></a>예

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings"></a>Ipv4 비교 같음-IP 접두사 표기법이 IPv4 문자열 내에 지정 되었습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings-and-an-additional-argument-of-the-ipv4_is_match-function"></a>Ipv4 비교 같음-IP 접두사 표기법이 IPv4 문자열 내에 지정 되 고 함수의 추가 인수 `ipv4_is_match()`

<!-- csl: https://help.kusto.windows.net/Samples -->
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
