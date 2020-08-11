---
title: format_ipv4_mask ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 format_ipv4_mask ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: ef0b8306ad5d62e7efa9cb037a6fb8d06d415859
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075423"
---
# <a name="format_ipv4_mask"></a>format_ipv4_mask ()

네트워크 마스크를 사용 하 여 입력을 구문 분석 하 고 IPv4 주소를 나타내는 문자열을 CIDR 표기법으로 반환 합니다.

```kusto
print format_ipv4_mask('192.168.1.255', 24) == '192.168.1.0/24'
print format_ipv4_mask(3232236031, 24) == '192.168.1.0/24'
```

## <a name="syntax"></a>구문

`format_ipv4_mask(`*Expr* [ `,` *PrefixMask*`])`

## <a name="arguments"></a>인수

* *`Expr`*: CIDR 표기법으로 된 IPv4 주소의 문자열 또는 숫자 표현입니다.
* *`PrefixMask`*: (선택 사항) 고려 되는 가장 중요 한 비트 수를 나타내는 0에서 32 사이의 정수입니다. 인수를 지정 하지 않으면 모든 비트 마스크 (32)가 사용 됩니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 IPv4 주소를 CIDR 표기법으로 나타내는 문자열이 반환 됩니다.
변환에 실패 하면 빈 문자열이 반환 됩니다.

## <a name="see-also"></a>참고 항목

- [format_ipv4 ()](format-ipv4-function.md): CIDR 표기법을 사용 하지 않는 ipv4 주소 형식입니다.
- [IPv4 및 IPv6 함수](scalarfunctions.md#ipv4ipv6-functions)

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(address:string, mask:long)
[
 '192.168.1.1', 24,          
 '192.168.1.1', 32,          
 '192.168.1.1/24', 32,       
 '192.168.1.1/24', long(-1), 
]
| extend result = format_ipv4(address, mask), 
         result_mask = format_ipv4_mask(address, mask)
```

|address|마스크|result|result_mask|
|---|---|---|---|
|192.168.1.1|24|192.168.1.0|192.168.1.0/24|
|192.168.1.1|32|192.168.1.1|192.168.1.1/32|
|192.168.1.1/24|32|192.168.1.0|192.168.1.0/24|
|192.168.1.1/24|-1|||
