---
title: array_rotate_left() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_rotate_left()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 82eb8c24a3ed04146e5416b020bda7085426d138
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518822"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`은 배열 내부의 값을 왼쪽으로 회전시다.

**구문**

`array_rotate_left(`*arr*, *rotate_count*`)`

**인수**

* *arr*: 분할 입력 배열, 동적 배열이어야합니다.
* *rotate_count*: 배열 요소가 왼쪽으로 회전할 위치 수를 지정하는 정수입니다. 값이 음수이면 요소가 오른쪽으로 회전합니다.

**반환**

각 요소가 *rotate_count*따라 회전된 원래 배열과 동일한 양의 요소를 포함하는 동적 배열 .

**참고 항목**

* 배열을 오른쪽으로 회전하려면 [array_rotate_right()](array_rotate_rightfunction.md)을 참조하십시오.
* 배열을 왼쪽으로 이동하려면 [array_shift_left()](array_shift_leftfunction.md)을 참조하십시오.
* 배열을 오른쪽으로 이동하려면 [array_shift_right()](array_shift_rightfunction.md)을 참조하십시오.

**예**

* 왼쪽으로 두 위치로 회전:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |도착|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|

* 음수 rotate_count 값을 사용하여 두 위치로 오른쪽으로 회전:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |도착|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|