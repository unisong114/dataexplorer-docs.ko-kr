---
title: array_shift_right ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_shift_right ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 714c6c15443420abbc973593acb2f311a5507dc4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225669"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`배열 내의 값을 오른쪽으로 이동 합니다.

**구문**

`array_shift_right(`*arr*, *shift_count* [, *fill_value* ]`)`

**인수**

* *arr*: 분할할 입력 배열입니다. 동적 배열 이어야 합니다.
* *shift_count*: 배열 요소가 오른쪽으로 이동할 위치 수를 지정 하는 정수입니다. 값이 음수 이면 요소가 왼쪽으로 이동 됩니다.
* *fill_value*: 이동 및 제거 된 요소 대신 요소를 삽입 하는 데 사용 되는 스칼라 값입니다. 기본값: *arr* 유형에 따라 null 값 또는 빈 문자열입니다.

**반환**

각 요소가 *shift_count*에 따라 이동한 원래 배열의 요소와 같은 양의 요소를 포함 하는 동적 배열입니다. 제거 된 요소 대신 추가 된 새 요소는 *fill_value*값을 갖습니다.

**참고 항목**

* 배열을 왼쪽으로 이동 하려면 [array_shift_left ()](array_shift_leftfunction.md)를 참조 하세요.
* 배열 오른쪽 회전의 경우 [array_rotate_right ()](array_rotate_rightfunction.md)를 참조 하세요.
* 배열의 회전을 왼쪽으로 [array_rotate_left ()](array_rotate_leftfunction.md)를 참조 하세요.

**예**

* 오른쪽에서 두 위치로 이동:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[null, null, 1, 2, 3]|

* 오른쪽에서 두 위치로 이동 하 고 기본값을 추가 합니다.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|


* 음수 shift_count 값을 사용 하 여 왼쪽으로 두 위치로 이동 합니다.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|
    