---
title: 변경 병합 테이블 열-문서 문자열 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 열-문서 문자열 변경에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75d298f35a215af5da443f673278e4a252c24cc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522392"
---
# <a name="alter-merge-table-column-docstrings"></a>테이블 열-문서 문자열 변경 병합

지정된 `docstring` 테이블의 하나 이상의 열의 속성을 설정합니다. 명시적으로 설정되지 **retain** 않은 열은 이 속성에 대한 기존 값을 유지합니다(열이 있는 경우).

테이블 열-문서 문자열 을 변경하면 [아래를](#alter-table-column-docstrings)참조하십시오.

**구문**

`.alter-merge``table` *표이름* `column-docstring` *Col1* `:` `:` `,` *Col2* *Docstring2* *Docstring1* 콜1 닥스트링1 [ 콜2 닥스트링2]... `(``)`

**예제** 

```
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>테이블 열-문서 문자열 변경

지정된 `docstring` 테이블의 하나 이상의 열의 속성을 설정합니다. 명시적으로 설정되지 않은 열에는 이 속성이 **제거됩니다.**

**구문**

`.alter``table` *표이름* `column-docstring` *Col1* `:` `:` `,` *Col2* *Docstring2* *Docstring1* 콜1 닥스트링1 [ 콜2 닥스트링2]... `(``)`

**예제** 

```
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```