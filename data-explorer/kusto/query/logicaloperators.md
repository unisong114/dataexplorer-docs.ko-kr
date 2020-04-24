---
title: 논리(바이너리) 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 논리(바이너리) 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 53505067b93234aa89849e1c66fe2f77a58e0f26
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513093"
---
# <a name="logical-binary-operators"></a>논리(이진) 연산자

다음 논리 연산자는 `bool` 형식의 두 값 사이에서 지원됩니다.

> [!NOTE]
> 이러한 논리 연산자는 부울 연산자라고도 하며 때로는 이진 연산자로 지칭됩니다. 이름은 모두 동의어입니다.

|운영자 이름|구문|의미|
|-------------|------|-------|
|같음     |`==`  |두 `true` 피연산자 모두 null이 아닌 경우 출력량이 서로 동일합니다. 그렇지 않으면 `false`입니다.|
|같지 않음   |`!=`  |피연산자의 하나(또는 둘 다)가 null이거나 서로 같지 않은 경우 수율입니다. `true` 그렇지 않으면 `true`입니다.|
|논리적 및  |`and` |두 `true` 피연산자 모두 `true`있는 경우 수율.|
|논리적 또는   |`or`  |피연산자 `true` `true`중 하나가 다른 피연산에 관계없이 수율입니다.|

> [!NOTE]
> Boolean null `bool(null)`값의 동작으로 인해 두 부울 null 값은 같지도 같지도 `bool(null) == bool(null)` `bool(null) != bool(null)` 같지도 않고 `false`같지도 않습니다(즉, 둘 다 값을 산출합니다).
>
> `and` / `or` 반면에 null 값을 `false`에 해당값으로 처리합니다. `bool(null) or true` `true` `bool(null) and true` `false`