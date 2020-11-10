---
title: 플러그 인 명령 플러그 인 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 플러그 인 관리 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1db761d8c290c7aaea452cd0cb3d85f2f648221c
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422119"
---
# <a name="show-plugins"></a>. 플러그 인 표시


클러스터의 모든 플러그 인을 나열 합니다.

## <a name="syntax"></a>구문

`.show` `plugins`

## <a name="output"></a>출력

다음 필드를 포함 하는 테이블을 반환 합니다.
* **Pluginname** : 플러그 인의 이름
* **IsEnabled** : 플러그 인을 사용할 수 있는지 여부를 나타내는 부울 값입니다.

## <a name="example"></a>예제

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | true  |

## <a name="next-steps"></a>다음 단계

* [. 플러그 인 사용 안 함](disable-plugin.md)
* [. 플러그 인 사용](enable-plugin.md)
