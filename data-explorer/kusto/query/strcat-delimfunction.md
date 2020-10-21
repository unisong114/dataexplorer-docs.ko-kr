---
title: strcat_delim ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 strcat_delim ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de34f4a2f6efb3af84b61f53da8d58cb60dea7ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243697"
---
# <a name="strcat_delim"></a>strcat_delim()

첫 번째 인수로 제공 되는 구분 기호를 사용 하 여 2 및 64 인수를 연결 합니다.

 * 인수가 문자열 형식이 아니면 문자열이 강제로 문자열로 변환 됩니다.

## <a name="syntax"></a>구문

`strcat_delim(`*delimiter*, *인수 1*, *인수 2*[, *argumentn*]`)`

## <a name="arguments"></a>인수

* *delimiter*: 구분 기호로 사용 되는 문자열 식입니다.
* *인수 1* ... *argumentn*: 연결할 식입니다.

## <a name="returns"></a>반환

인수는 *구분 기호*를 사용 하 여 단일 문자열에 연결 됩니다.

## <a name="examples"></a>예

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
