---
title: parse_ipv4_mask ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 parse_ipv4_mask () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 7399bf83369a1b1e1565dd127712faccf7e60830
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346476"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

IPv4 및 네트워크 마스크의 입력 문자열을 long 숫자 표현 (부호 있는 64 비트)으로 변환 합니다.

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

## <a name="syntax"></a>구문

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>인수

* *`Expr`*: Long으로 변환 될 IPv4 주소의 문자열 표현입니다. 
* *`PrefixMask`*: 고려 되는 가장 중요 한 비트 수를 나타내는 0에서 32 사이의 정수입니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과가 길어질 수 있습니다.
변환이 성공 하지 못하면 결과는가 됩니다 `null` .
