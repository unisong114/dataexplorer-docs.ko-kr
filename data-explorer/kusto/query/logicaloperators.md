---
title: 논리 (이진) 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 논리적 (이진) 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 6d1fcf7b9786951fedbdbf45d9e2c20a765452dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248511"
---
# <a name="logical-binary-operators"></a>논리(이진) 연산자

다음 논리 연산자는 형식의 두 값 사이에서 지원 됩니다 `bool` .

> [!NOTE]
> 이러한 논리 연산자를 부울 연산자 라고 하며 때로는 이항 연산자로도 사용할 수 있습니다. 이름은 모두 동의어입니다.

|운영자 이름|구문|의미|
|-------------|------|-------|
|등호     |`==`  |`true`두 피연산자가 모두 null이 아니고 서로 같은지 여부를 생성 합니다. 그렇지 않으면 `false`입니다.|
|같지 않음   |`!=`  |`true`피연산자 중 하나 (또는 둘 다)가 null 이거나 서로 같지 않은 경우를 생성 합니다. 그렇지 않으면 `true`입니다.|
|논리적 and  |`and` |`true`두 피연산자가 모두 이면를 생성 `true` 합니다.|
|논리적 or   |`or`  |`true`피연산자 중 하나가 `true` 다른 피연산자에 관계 없이 인 경우를 생성 합니다.|

> [!NOTE]
> 부울 null 값의 동작 때문에 `bool(null)` 두 부울 null 값은 같지 않으며 같지 않습니다 (즉, `bool(null) == bool(null)` `bool(null) != bool(null)` 둘 다 값을 생성 함 `false` ).
>
> 반면에 null 값을와 동일 하 게 `and` / `or` 처리 합니다 `false` `bool(null) or true` . 즉,는 이며 `true` `bool(null) and true` 은 `false` 입니다.