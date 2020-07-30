---
title: 변환 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 변환 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: d0e99048f3f1b0e3ce5c6c59a65ea645b22d15fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340057"
---
# <a name="translate"></a>translate()

지정 된 문자열에서 문자 집합 (' searchList ')을 다른 문자 집합 (' replacementList ')으로 바꿉니다.
함수는 ' searchList '의 문자를 검색 하 고 ' replacementList '의 해당 문자로 바꿉니다.

## <a name="syntax"></a>구문

`translate(`*Searchlist* `,` *replacementList* `,` *텍스트*`)`

## <a name="arguments"></a>인수

* *searchlist*: 바꾸어야 하는 문자 목록입니다.
* *replacementList*: ' searchlist '의 문자를 대체 해야 하는 문자 목록입니다.
* *text*: 검색할 문자열입니다.

## <a name="returns"></a>반환

' replacementList '의 모든 문자를 ' searchList '의 해당 문자로 바꾼 후의 *텍스트* 입니다.

## <a name="examples"></a>예

|입력                                 |출력   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|