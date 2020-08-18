---
title: series_ifft ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_ifft () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: c5862e9c18959726f27ea7d4237058f36a408b5c
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502165"
---
# <a name="series_ifft"></a>series_ifft ()

계열에 역 IFFT (빠른 푸리에 변환)를 적용 합니다.  

Series_ifft () 함수는 frequency 도메인에서 일련의 복소수를 사용 하 고 [Fast 푸리에 변환을](https://en.wikipedia.org/wiki/Fast_Fourier_transform)사용 하 여 시간/공간 도메인으로 다시 변환 합니다. 이 함수는 [series_fft](series-fft-function.md)의 보완 함수입니다. 일반적으로 원래 계열은 spectral 처리를 위해 frequency 도메인으로 변환 된 다음 다시 시간/공간 도메인으로 변환 됩니다.

## <a name="syntax"></a>구문

`series_ifft(`*fft_real* [ `,` *fft_imaginary*]`)`

## <a name="arguments"></a>인수

* *fft_real*: 변환할 계열의 실제 구성 요소를 나타내는 숫자 값의 동적 배열입니다.
* *fft_imaginary*: 계열의 허수 구성 요소를 나타내는 유사한 동적 배열입니다. 이 매개 변수는 선택 사항이 며 입력 계열에 복소수를 포함 하는 경우에만 지정 해야 합니다.

## <a name="returns"></a>반환

함수는 두 계열의 복잡 한 역 fft을 반환 합니다. 실제 구성 요소에 대 한 첫 번째 계열과 허수 구성 요소에 대 한 첫 번째 계열입니다.

## <a name="example"></a>예제

[Series_fft](series-fft-function.md#example) 참조
