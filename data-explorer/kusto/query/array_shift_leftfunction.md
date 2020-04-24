---
title: array_shift_left() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_shift_left()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 5b623bdcccc75261d0fb9324bb6015e16b0ff9b8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518788"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()`은 배열 내부의 값을 왼쪽으로 이동합니다.

**구문**

`array_shift_left(`*arr*, *shift_count* [, *fill_value]*`)`

**인수**

* *arr*: 분할 입력 배열, 동적 배열이어야합니다.
* *shift_count*: 배열 요소가 왼쪽으로 이동되는 위치 수를 지정하는 정수입니다. 값이 음수이면 요소가 오른쪽으로 이동됩니다.
* *fill_value*: 이동 및 제거된 요소 대신 요소를 삽입하는 데 사용되는 스칼라 값입니다. 기본값: null 값 또는 빈 *문자열(arr* 유형에 따라 다름).

**반환**

각 요소가 *shift_count*따라 이동된 원래 배열과 동일한 양의 요소를 포함하는 동적 배열 . 제거된 요소 대신 추가되는 새 요소에는 *fill_value.*

**참고 항목**

* 배열을 오른쪽으로 이동하면 [array_shift_right()](array_shift_rightfunction.md)을 참조하십시오.
* 배열을 오른쪽으로 회전하는 경우 [array_rotate_right()](array_rotate_rightfunction.md)을 참조하십시오.
* 왼쪽 배열회전은 [array_rotate_left() 을](array_rotate_leftfunction.md)참조하십시오.

**예**

* 왼쪽으로 두 위치로 이동:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |도착|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5, 널, 널]|

* 두 위치로 왼쪽으로 이동하고 기본값을 추가합니다.

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |도착|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|


* 음수 *shift_count* 값을 사용하여 두 위치로 오른쪽으로 이동:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |도착|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|
