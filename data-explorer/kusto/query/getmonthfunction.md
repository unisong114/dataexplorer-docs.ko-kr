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
ms.openlocfilehash: 13254314bdbab7ddbdb74341e384867c26b6259d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347700"
---
# <a name="getmonth"></a>getmonth()

datetime에서 월 번호(1-12)를 가져옵니다.

다른 별칭: monthoyear ()

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
