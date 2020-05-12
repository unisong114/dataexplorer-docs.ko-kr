---
title: getmonth ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 getmonth ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: e04abb6eed95e5129d6878486e3781173cbf0557
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226791"
---
# <a name="getmonth"></a>getmonth()

datetime에서 월 번호(1-12)를 가져옵니다.

다른 별칭: monthoyear ()

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
