---
title: array_rotate_right ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_rotate_right ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: c74dd0aacd4360e601ec58c6a767debadcbc2d05
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249420"
---
# <a name="array_rotate_right"></a>array_rotate_right()

배열 내의 값 `dynamic` 을 오른쪽으로 회전 합니다.

## <a name="syntax"></a>구문

`array_rotate_right(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>인수

* *arr*: 분할할 입력 배열입니다. 동적 배열 이어야 합니다.
* *rotate_count*: 배열 요소가 오른쪽으로 회전 되는 위치의 수를 지정 하는 정수입니다. 값이 음수 이면 요소가 왼쪽으로 회전 됩니다.

## <a name="returns"></a>반환

각 요소가 *rotate_count*에 따라 회전 된 원래 배열과 동일한 양의 요소를 포함 하는 동적 배열입니다.

## <a name="see-also"></a>참조

* 배열을 왼쪽으로 회전 하는 경우 [array_rotate_left ()](array_rotate_leftfunction.md)를 참조 하세요.
* 배열을 왼쪽으로 이동 하려면 [array_shift_left ()](array_shift_leftfunction.md)를 참조 하세요.
* 배열을 오른쪽으로 이동 하려면 [array_shift_right ()](array_shift_rightfunction.md)를 참조 하세요.

## <a name="examples"></a>예

* 오른쪽에서 두 위치로 회전:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|

* 음수 rotate_count 값을 사용 하 여 왼쪽으로 두 위치 회전:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|
