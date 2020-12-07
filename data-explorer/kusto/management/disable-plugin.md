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
ms.openlocfilehash: 1063355f880f26a321eb6416180ae9764575f0be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320896"
---
# <a name="disable-plugin"></a>.disable 플러그 인

플러그 인을 사용 하지 않도록 설정 합니다.

이 명령을 `All Databases admin` 사용 하려면 권한이 필요 합니다.

## <a name="syntax"></a>Syntax

`.disable``plugin` *Pluginname*

## <a name="example"></a>예제
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>다음 단계

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

