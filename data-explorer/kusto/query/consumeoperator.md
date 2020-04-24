---
title: 소모 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 사용 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 65c2f2befc074042131b5c0d705fa942a1622035
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517122"
---
# <a name="consume-operator"></a>consume 연산자

연산자에게 전달되는 테이블 형식 데이터 스트림을 사용합니다. 

연산자는 `consume` 결과를 실제로 호출자에게 반환하지 않고 쿼리 부작용을 트리거하는 데 주로 사용됩니다.

```kusto
T | consume
```

**구문**

`consume`[`decodeblocks` `=` *디코딩 블록*]

**인수**

* *디코딩 블록*: 상수 부울 값입니다. 로 `true`설정되거나 요청 속성이 `perftrace` `true`로 설정된 `consume` 경우 연산자는 입력에서 레코드를 등록할 뿐만 아니라 실제로 해당 레코드의 각 값을 압축 해제 및 디코딩하도록 강제합니다.

운영자는 `consume` 결과를 클라이언트에 실제로 다시 제공하지 않고 쿼리 비용을 추정하는 데 사용할 수 있습니다.
(추정은 여러 가지 이유로 정확하지 않습니다. 예를 `consume` 들어, 배포하여 계산되므로 `T | consume` 클러스터의 노드 간에 테이블의 데이터를 전송하지 않습니다.)

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->