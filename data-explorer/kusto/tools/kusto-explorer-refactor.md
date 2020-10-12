---
title: Kusto 탐색기 코드 리팩터링-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 탐색기의 코드 리팩터링에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 959cf8d25b20d459b48a0c8f1968541b50917a9d
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942236"
---
# <a name="kusto-explorer-code-refactoring"></a>Kusto 탐색기 코드 리팩터링

다른 Ide와 마찬가지로, Kusto 탐색기는 KQL 쿼리 편집 및 리팩터링을 위한 몇 가지 기능을 제공 합니다.

## <a name="rename-variable-or-column-name"></a>변수 또는 열 이름 이름 바꾸기

`Ctrl` + `R` `Ctrl` + `R` 쿼리 편집기 창에서를 클릭 하면 현재 선택한 기호의 이름을 바꿀 수 있습니다.

환경을 보여 주는 아래 스냅숏을 참조 하세요.

![쿼리 편집기 창에서 이름을 바꾸는 변수를 보여 주는 애니메이션 GIF입니다. 세 번의 발생은 동시에 새 이름으로 바뀝니다.](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "리팩터링-이름 바꾸기")

## <a name="extract-scalars-as-let-expressions"></a>식으로 스칼라 추출 `let`

를 클릭 하 여 현재 선택한 리터럴을 식으로 승격할 수 있습니다 `let` `Alt` + `Ctrl` + `M` . 

![애니메이션 GIF. 리터럴 식에 대해 쿼리 편집기 포인터가 시작 됩니다. 그러면 리터럴 값을 새 변수로 설정 하는 let 문이 표시 됩니다.](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "let으로 추출-리터럴")

## <a name="extract-tabular-statements-as-let-expressions"></a>테이블 형식 문을 식으로 추출 합니다. `let`

`let`텍스트를 선택한 다음를 클릭 하 여 테이블 형식 식을 문으로 승격할 수도 있습니다 `Alt` + `Ctrl` + `M` . 

![애니메이션 GIF. 쿼리 편집기에서 테이블 형식 식이 선택 됩니다. 그러면 해당 테이블 형식 식을 새 변수로 설정 하는 let 문이 표시 됩니다.](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "extract as-테이블 형식")
