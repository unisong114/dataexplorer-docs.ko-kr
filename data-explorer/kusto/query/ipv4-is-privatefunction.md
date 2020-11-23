---
title: ipv4_is_private ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 ipv4_is_private () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 161e0a2ca94bb9b037fc619c0fc5dfbddd8d28da
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324917"
---
# <a name="ipv4_is_private"></a>ipv4_is_private ()

IPv4 문자열 주소가 개인 네트워크 Ip 집합에 속하는지 확인 합니다.

[개인 네트워크 주소](https://en.wikipedia.org/wiki/Private_network) 는 원래 IPv4 주소 소모를 지연 하는 데 도움이 되도록 정의 되었습니다. 개인 IP 주소로 시작 되거나 주소가 지정 된 IP 패킷은 공용 인터넷을 통해 라우팅할 수 없습니다.

## <a name="private-ipv4-addresses"></a>개인 IPv4 주소

IETF (Internet 엔지니어링 작업 Force)는 개인 네트워크에 대해 다음과 같은 IPv4 주소 범위를 예약 하도록 IANA (인터넷 할당 번호 기관)에 지시 했습니다.

| IP 주소 범위|주소 수|가장 큰 CIDR 블록 (서브넷 마스크)|
|-----------------|-------------------|--------------------------------|
|10.0.0.0 – 10.255.255.255|16777216|10.0.0.0/8 (255.0.0.0)|
|172.16.0.0 – 172.31.255.255|1048576|172.16.0.0/12 (255.240.0.0)|
|192.168.0.0 – 192.168.255.255|65536|192.168.0.0/16 (255.255.0.0)|


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>구문

`ipv4_is_private(`*Expr*`)`

## <a name="arguments"></a>인수

*Expr*: IPv4 주소를 나타내는 문자열 식입니다. [IP 접두사 표기법](#ip-prefix-notation)을 사용 하 여 IPv4 문자열을 마스킹할 수 있습니다.

### <a name="ip-prefix-notation"></a>IP 접두사 표기법

IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` . 슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 32)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다. 

예를 들어 192.168.2.0/24에는 24 개의 연속 비트를 포함 하는 연결 된 net/subnetmask와 점으로 구분 된 10 진수 형식의 255.255.255.0이 있습니다.

## <a name="returns"></a>반환

* `true`: IPv4 주소가 개인 네트워크 범위에 속하는 경우
*  `false`그렇지.
* `null`: 두 IPv4 문자열 중 하나에 대 한 변환이 실패 한 경우

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>예: IPv4가 개인 네트워크에 속해 있는지 확인

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|result|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
