---
title: 제한 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 제한 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32b31fbef64d9ea689f427ea3b8ebd6fb0287090
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513297"
---
# <a name="limit-operator"></a>limit 연산자

지정된 행 수로 되돌아갑니다.

```kusto
T | limit 5
```

**별칭**

[take 연산자](takeoperator.md)