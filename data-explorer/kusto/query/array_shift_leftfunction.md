---
title: array_shift_left ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_shift_left ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4bdadd276a59b30ed347a3e293eb5e5c7831063b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247035"
---
# <a name="array_shift_left"></a>array_shift_left()

배열 내의 값을 `dynamic` 왼쪽으로 이동 합니다.

## <a name="syntax"></a>구문

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>인수

* *`arr`*: 분할할 입력 배열입니다. 동적 배열 이어야 합니다.
* *`shift_count`*: 배열 요소를 왼쪽으로 이동할 위치 수를 지정 하는 정수입니다. 값이 음수 이면 요소가 오른쪽으로 이동 합니다.
* *`fill_value`*: 이동 및 제거 된 요소 대신 요소를 삽입 하는 데 사용 되는 스칼라 값입니다. 기본값: 형식에 따라 null 값 또는 빈 문자열 *`arr`* 입니다.

## <a name="returns"></a>반환

원래 배열과 동일한 수의 요소를 포함 하는 동적 배열입니다. *Shift_count*에 따라 각 요소가 이동 되었습니다. 제거 된 요소 대신 추가 된 새 요소는 *fill_value*값을 갖습니다.

## <a name="see-also"></a>참조

* 배열 이동에 대 한 자세한 내용은 [array_shift_right ()](array_shift_rightfunction.md)를 참조 하세요.
* 배열 오른쪽 회전의 경우 [array_rotate_right ()](array_rotate_rightfunction.md)를 참조 하세요.
* 배열의 회전을 왼쪽으로 [array_rotate_left ()](array_rotate_leftfunction.md)를 참조 하세요.

## <a name="examples"></a>예

* 왼쪽에서 두 위치로 이동:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, null, null]|

* 왼쪽에서 두 위치로 이동 하 고 기본값을 추가 합니다.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|


* 음수 *shift_count* 값을 사용 하 여 오른쪽에서 두 위치로 이동 합니다.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|
