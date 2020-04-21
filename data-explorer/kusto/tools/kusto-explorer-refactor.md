---
title: 쿠스토 탐색기 코드 리팩터링 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto 탐색기 코드 리팩터링에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 0a89cf9c648fc4811d56c22012cdb25d5505eb4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523990"
---
# <a name="kusto-explorer-code-refactoring"></a>쿠스토 탐색기 코드 리팩터링

다른 IID와 마찬가지로 Kusto.Explorer는 KQL 쿼리 편집 및 리팩터링에 몇 가지 기능을 제공합니다.

## <a name="rename-variable-or-column-name"></a>변수 또는 열 이름 바꾸기

`Ctrl` + `R`을 `Ctrl` + 쿼리 편집기 창에서 현재 선택한 기호의 이름을 바꿀 수 `R` 있습니다.

아래는 경험을 보여 주는 스냅샷을 참조하십시오.

![대체 텍스트](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "리팩터 링-이름 바꾸기")

## <a name="extract-scalars-as-let-expressions"></a>표현식으로 `let` 스칼라 추출

을 `let` 클릭하여 `Alt` + `Ctrl` +현재 선택한 리터럴을 표현식으로 승격할 수 있습니다. `M` 

![대체 텍스트](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "리터럴로 추출")

## <a name="extract-tabular-statements-as-let-expressions"></a>`let` 표문문을 식으로 추출

텍스트를 선택한 다음 을 `let` 클릭하여 `Alt` + `Ctrl` + `M`표식 식을 문으로 승격할 수도 있습니다. 

![대체 텍스트](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "추출-로-let-테이블 형식")
