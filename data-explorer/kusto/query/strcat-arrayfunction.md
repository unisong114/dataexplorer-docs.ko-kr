---
title: strcat_array ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 strcat_array ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b8369d2e994477c9d01880632fac5f8a3ebaf6a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342600"
---
# <a name="strcat_array"></a>strcat_array()

지정 된 구분 기호를 사용 하 여 배열 값의 연결 된 문자열을 만듭니다.
    
## <a name="syntax"></a>Syntax

`strcat_array(`*배열*, *구분 기호*`)`

## <a name="arguments"></a>인수

* *array*: `dynamic` 연결할 값의 배열을 나타내는 값입니다.
* *구분 기호가*: `string` *배열* 에서 값을 연결 하는 데 사용 되는 값입니다.

## <a name="returns"></a>반환

단일 문자열에 연결 된 배열 값입니다.

## <a name="examples"></a>예제
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|