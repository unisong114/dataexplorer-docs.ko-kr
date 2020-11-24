---
title: ipv4_lookup 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 ipv4_lookup 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: ec826e7d16e575fa4904aba93575ab7e605d2b18
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511155"
---
# <a name="ipv4_lookup-plugin"></a>ipv4_lookup 플러그 인

`ipv4_lookup`플러그 인이 조회 테이블에서 IPv4 값을 조회 하 고 일치 하는 값을 가진 행을 반환 합니다.

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>구문

*T* `|` `evaluate` `ipv4_lookup(` *LookupTable* `,` *SourceIPv4Key* `,` *LookupKey* [ `,` *return_unmatched* ] `)`

## <a name="arguments"></a>인수

* *T*: 열 *SourceIPv4Key* 이 IPv4 일치에 사용 되는 테이블 형식 입력입니다.
* *LookupTable*: ipv4 조회 데이터를 사용 하는 테이블 또는 테이블 형식 식으로, *LookupKey* 열은 ipv4 일치에 사용 됩니다. [IP 접두사 표기법](#ip-prefix-notation)을 사용 하 여 IPv4 값을 마스킹할 수 있습니다.
* *SourceIPv4Key*: *LookupTable* 에서 조회할 IPv4 문자열이 있는 *T* 의 열입니다. [IP 접두사 표기법](#ip-prefix-notation)을 사용 하 여 IPv4 값을 마스킹할 수 있습니다.
* *LookupKey*: 각 *SourceIPv4Key* 값과 일치 하는 IPv4 문자열이 있는 *LookupTable* 의 열입니다.
* *return_unmatched*: 결과가 전체 또는 일치 하는 행만 포함 해야 하는지 여부를 정의 하는 부울 플래그입니다. 기본값: `false` -일치 하는 행만 반환 합니다.

### <a name="ip-prefix-notation"></a>IP 접두사 표기법
 
IP 주소 `IP-prefix notation` 는 슬래시 () 문자를 사용 하 여 정의할 수 있습니다 `/` .
슬래시 ()의 왼쪽에 있는 IP 주소는 `/` 기본 ip 주소입니다. 슬래시 ()의 오른쪽에 있는 숫자 (1 ~ 32)는 `/` 네트워크 마스크에서 연속 된 1 비트의 수입니다. 

예를 들어 192.168.2.0/24에는 24 개의 연속 비트를 포함 하는 연결 된 net/subnetmask와 점으로 구분 된 10 진수 형식의 255.255.255.0이 있습니다.

## <a name="returns"></a>반환

`ipv4_lookup`플러그 인은 IPv4 키를 기반으로 조인 (조회) 결과를 반환 합니다. 테이블의 스키마는 [ `lookup` 연산자](lookupoperator.md)의 결과와 유사 하 게 원본 테이블과 조회 테이블의 합집합입니다.

*Return_unmatched* 인수가로 설정 되 면 `true` 결과 테이블에 일치 하는 행과 일치 하지 않는 행이 모두 포함 됩니다 (null로 채워짐).

*Return_unmatched* 인수를로 설정 하거나 생략 하는 경우 `false` (의 기본값을 사용 하는 경우 `false` ) 결과 테이블의 레코드 수가 일치 하는 결과와 동일 하 게 됩니다. 이 조회 변형은 실행에 비해 성능이 더 우수 `return_unmatched=true` 합니다.

> [!NOTE]
> * 이 플러그 인은 작은 조회 테이블 크기 (10만 계의 행)를 포함 하 고 필요에 따라 입력 테이블의 크기가 더 큰 경우에만 IPv4 기반 조인의 시나리오를 다룹니다.
> * 플러그 인의 성능은 조회 및 데이터 원본 테이블의 크기, 열 수 및 일치 하는 레코드 수에 따라 달라 집니다.

## <a name="examples"></a>예

### <a name="ipv4-lookup---matching-rows-only"></a>IPv4 조회-일치 하는 행만

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|ip|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|유럽|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|유럽|RU|러시아|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>IPv4 조회-일치 하는 행과 일치 하지 않는 행을 모두 반환 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|유럽|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|유럽|RU|러시아|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>IPv4 조회-external_data ()의 원본 사용

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|network|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|유럽|GB|United Kingdom|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|유럽|RU|러시아|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|유럽|SE|스웨덴|0|0|
