---
title: sign ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 서명 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6761cc2ffa9a8c28151c00720bbd1340a56875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351083"
---
# <a name="sign"></a>sign()

숫자 식의 부호

## <a name="syntax"></a>Syntax

`sign(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수입니다.

## <a name="returns"></a>반환

* 지정 된 식의 양수 (+ 1), 0 또는 음수 (-1) 기호입니다. 

## <a name="examples"></a>예제

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|