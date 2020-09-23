---
title: log10 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 log10 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 62813f5680e89c2bca0a6ec547fd7055ddc0e414
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103191"
---
# <a name="log10"></a>log10()

`log10()` 일반 (밑수 10) 로그 함수를 반환 합니다.  

## <a name="syntax"></a>구문

`log10(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수 > 0입니다.

## <a name="returns"></a>반환

* 일반적인 로그는 밑이 10 인 지 수 함수 (exp)의 역함수 인 상용 로그입니다.
* `null` 인수가 음수 이거나 null 이거나 값으로 변환할 수 없는 경우 `real` 입니다. 

## <a name="see-also"></a>참고 항목

* 자연 (밑수-e) [로그는 log ()](log-function.md)를 참조 하세요.
* 밑이 2 인 경우 [log2 ()](log2-function.md) 를 참조 하세요.