---
title: todynamic (), todynamic ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 todynamic (), todynamic ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: acd1b5328150e61bc81930f94b8ea9e8025e1ebb
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804086"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

는를 `string` [JSON 값](https://json.org/) 으로 해석 하 고 값을로 반환 합니다 [`dynamic`](./scalar-data-types/dynamic.md) . 

JSON 복합 개체의 요소를 둘 이상 추출 해야 하는 경우 [extractjson () 함수](./extractjsonfunction.md) 를 사용 하는 것이 더 우수 합니다.

[Parse_json ()](./parsejsonfunction.md) 함수에 대 한 별칭입니다.

> [!NOTE]
> 가능 하면 [dynamic ()을](./scalar-data-types/dynamic.md) 사용 하는 것이 좋습니다.

## <a name="syntax"></a>구문

`todynamic(`*json* `)` 
 json `toobject(` *json*`)`

## <a name="arguments"></a>인수

* *json*: json 문서입니다.

## <a name="returns"></a>반환

*json*에 의해 지정된 형식 `dynamic`의 개체입니다.
