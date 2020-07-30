---
title: 사용 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 사용 연산자를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 85fd891590e359e31224ed5d707a837b1cc0eb41
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348839"
---
# <a name="consume-operator"></a>consume 연산자

연산자에 전달 되는 테이블 형식 데이터 스트림을 사용 합니다. 

`consume`연산자는 실제로 호출자에 게 결과를 반환 하지 않고 쿼리 파생 효과를 트리거하는 데 주로 사용 됩니다.

```kusto
T | consume
```

## <a name="syntax"></a>Syntax

`consume`[ `decodeblocks` `=` *DecodeBlocks*]

## <a name="arguments"></a>인수

* *DecodeBlocks*: 상수 부울 값입니다. 로 설정 된 경우 `true` 또는 요청 속성이로 설정 된 경우 연산자는 해당 입력에서 레코드를 열거 하는 것이 아니라 `perftrace` `true` `consume` 실제로 해당 레코드의 각 값의 압축을 해제 하 고 디코딩하는 것을 강제로 적용 합니다.

연산자를 사용 하 여 `consume` 실제로 결과를 클라이언트에 배달 하지 않고도 쿼리 비용을 추정할 수 있습니다.
예측은 다양 한 이유로 정확 하지 않습니다. 예를 들어 `consume` 는 distributively 계산 되므로는 `T | consume` 클러스터의 노드 간에 테이블의 데이터를 전송 하지 않습니다.

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->