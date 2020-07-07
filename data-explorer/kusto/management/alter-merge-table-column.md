---
title: alter-merge 테이블 열-docstrings-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter-merge 테이블 열 docstrings에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f71a46e6c747b7f9d9f6a3ba2d2f8a308635128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967216"
---
# <a name="alter-merge-table-column-docstrings"></a>.alter-merge table column-docstrings

지정 된 테이블에 있는 하나 이상의 열에 대 한 속성을 설정 합니다 `docstring` . 명시적으로 설정 되지 않은 열에는이 속성에 대 한 기존 값 (있는 경우)이 **유지** 됩니다.

Alter table column-docstring의 경우 [아래](#alter-table-column-docstrings)를 참조 하세요.

**구문**

`.alter-merge``table` *TableName* `column-docstring` TableName `(` *Col1* `:` *Docstring1* [ `,` *Col2* `:` *Docstring2*] ...`)`

**예제** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>alter table 열-docstrings

지정 된 테이블에 있는 하나 이상의 열에 대 한 속성을 설정 합니다 `docstring` . 명시적으로 설정 하지 않은 열에는이 속성이 **제거**됩니다.

**구문**

`.alter``table` *TableName* `column-docstring` TableName `(` *Col1* `:` *Docstring1* [ `,` *Col2* `:` *Docstring2*] ...`)`

**예제** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
