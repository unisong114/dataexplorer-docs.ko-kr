---
title: strcat_delim ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 strcat_delim ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2568196dc20042e95521ed0818bd625f3394599b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342566"
---
# <a name="strcat_delim"></a>strcat_delim()

첫 번째 인수로 제공 되는 구분 기호를 사용 하 여 2 및 64 인수를 연결 합니다.

 * 인수가 문자열 형식이 아니면 문자열이 강제로 문자열로 변환 됩니다.

## <a name="syntax"></a>Syntax

`strcat_delim(`*delimiter*, *인수 1*, *인수 2*[, *argumentn*]`)`

## <a name="arguments"></a>인수

* *delimiter*: 구분 기호로 사용 되는 문자열 식입니다.
* *인수 1* ... *argumentn*: 연결할 식입니다.

## <a name="returns"></a>반환

인수는 *구분 기호*를 사용 하 여 단일 문자열에 연결 됩니다.

## <a name="examples"></a>예제

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
