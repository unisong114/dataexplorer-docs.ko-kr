---
title: parse_ipv4 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_ipv4 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: f5d65fa75a4f53bea7d27202c5aed2228c167bbd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250915"
---
# <a name="parse_ipv4"></a>parse_ipv4()

IPv4 문자열을 long (부호 있는 64 비트) 숫자 표현으로 변환 합니다.

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

## <a name="syntax"></a>구문

`parse_ipv4(`*`Expr`*`)`

## <a name="arguments"></a>인수

* *`Expr`*: Long으로 변환 될 IPv4를 나타내는 문자열 식입니다. 문자열에는 [IP 접두사 표기법](#ip-prefix-notation)을 사용한 네트 마스크가 포함 될 수 있습니다.

## <a name="ip-prefix-notation"></a>IP 접두사 표기법

IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` .
슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 (/)의 오른쪽에 있는 숫자 (1 ~ 32)는 네트워크 마스크에서 연속 된 1 비트의 수입니다.

예를 들어 192.168.2.0/24에는 24 개의 연속 비트를 포함 하는 연결 된 net/subnetmask와 점으로 구분 된 10 진수 형식의 255.255.255.0이 있습니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과가 길어질 수 있습니다.
변환이 성공 하지 못하면 결과는가 됩니다 `null` .
 
## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
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
