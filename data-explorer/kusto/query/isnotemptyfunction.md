---
title: isnotempty ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 isnotempty ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f7811c2668bd0bb2d6e3711800ee5d9a450b9ec
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056237"
---
# <a name="isnotempty"></a>isnotempty()

`true`인수가 빈 문자열이 아니며 null이 아닌 경우를 반환 합니다.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>구문

`isnotempty(`[*값*]`)`

`notempty(`[*값*] `)` --별칭 `isnotempty`

## <a name="example"></a>예제

```kusto
T
| where isnotempty(fieldName)
| count
```
