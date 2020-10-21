---
title: getmonth ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 getmonth ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 1b3f87c7be8f4b2e12fc0136c163c4df0766eed8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252985"
---
# <a name="getmonth"></a>getmonth()

datetime에서 월 번호(1-12)를 가져옵니다.

다른 별칭: monthoyear ()

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
