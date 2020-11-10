---
title: 플러그 인 명령 사용 안 함-Azure 데이터 탐색기
description: 이 문서에서는 플러그 인 관리 명령을 설명 합니다. Azure 데이터 탐색기에서 플러그 인을 사용 하지 않도록 설정 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1dd2649d4746506f0ef2c08d4615260babd594e1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422114"
---
# <a name="disable-plugin"></a>. 플러그 인 사용 안 함

플러그 인을 사용 하지 않도록 설정 합니다.

이 명령을 `All Databases admin` 사용 하려면 권한이 필요 합니다.

## <a name="syntax"></a>구문

`.disable``plugin` *Pluginname*

## <a name="example"></a>예제
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>다음 단계

* [. 플러그 인 표시](show-plugins.md)
* [. 플러그 인 사용](enable-plugin.md)

