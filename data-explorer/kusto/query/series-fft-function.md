---
title: series_fft ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_fft () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 32b3b978f57f1a346ccd697fa2d95d962b558a15
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502176"
---
# <a name="series_fft"></a>series_fft ()

계열에 FFT (Fast 푸리에 변환)를 적용 합니다.  

Series_fft () 함수는 시간/공간 도메인에서 일련의 복소수를 사용 하 고 [Fast 푸리에 변환을](https://en.wikipedia.org/wiki/Fast_Fourier_transform)사용 하 여 frequency 도메인으로 변환 합니다. 변환 된 복합 계열은 원래 계열에 표시 되는 빈도의 크기와 단계를 나타냅니다. 보완 함수 [series_ifft](series-ifft-function.md) 를 사용 하 여 frequency 도메인에서 시간/공간 도메인으로 다시 변환 합니다.

## <a name="syntax"></a>구문

`series_fft(`*x_real* [ `,` *x_imaginary*]`)`

## <a name="arguments"></a>인수

* *x_real*: 변환할 계열의 실제 구성 요소를 나타내는 숫자 값의 동적 배열입니다.
* *x_imaginary*: 계열의 허수 구성 요소를 나타내는 유사한 동적 배열입니다. 이 매개 변수는 선택 사항이 며 입력 계열에 복소수를 포함 하는 경우에만 지정 해야 합니다.

## <a name="returns"></a>반환

함수는 두 계열의 복잡 한 역 fft을 반환 합니다. 실제 구성 요소에 대 한 첫 번째 계열과 허수 구성 요소에 대 한 첫 번째 계열입니다.

## <a name="example"></a>예제

* 실수부 및 허수 구성 요소가 서로 다른 빈도로 순수 사인 물결 인 복합 계열을 생성 합니다. FFT를 사용 하 여 frequency 도메인으로 변환 합니다.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | render linechart with(ysplit=panels)
    ```
    
    이 쿼리는 *fft_y_real* 을 반환 하 고 *fft_y_imag*합니다.  
    
    :::image type="content" source="images/series-fft-function/series-fft.png" alt-text="계열 fft" border="false":::
    
* 시리즈를 frequency 도메인으로 변환 하 고 역 변환을 적용 하 여 원래 계열을 다시 가져옵니다.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | extend (y_real2, y_image2) = series_ifft(fft_y_real, fft_y_imag)
    | project-away fft_y_real, fft_y_imag   //  too many series for linechart with panels
    | render linechart with(ysplit=panels)
    ```
    
    이 쿼리는 *y_real* 및 *y_imag*와 동일한 *y_real2* 및 * y_imag2를 반환 합니다.  
    
    :::image type="content" source="images/series-fft-function/series-ifft.png" alt-text="계열 ifft" border="false":::
    