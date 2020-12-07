---
title: 함수 라이브러리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기 기능을 확장 하는 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fb08edf4105c44a6be96cf6b2f314cf25887e69a
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443986"
---
# <a name="functions-library"></a>함수 라이브러리

다음 문서에는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)의 범주별 목록이 포함 되어 있습니다.

사용자 정의 함수 코드는 문서에 제공 됩니다.  쿼리에 포함 된 let 문 내에서 사용 하거나를 사용 하 여 데이터베이스에 유지할 수 있습니다 [`.create function`](../management/create-function.md) .

## <a name="machine-learning-functions"></a>기계 학습 함수

|함수 이름     |설명                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|K를 사용 하는 것은 알고리즘을 사용 하는 것입니다. |
|[predict_fl()](predict-fl.md)|기존의 학습 된 기계 학습 모델을 사용 하 여 예측 합니다. |
|[predict_onnx_fl()](predict-onnx-fl.md)| ONNX 형식으로 기존의 학습 된 기계 학습 모델을 사용 하 여 예측 합니다. |

## <a name="series-processing-functions"></a>계열 처리 함수

|함수 이름     |설명                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|양자화 metric 열. |
|[series_dot_product_fl()](series-dot-product-fl.md)|두 숫자 벡터의 내적을 계산 합니다. |
|[series_downsample_fl()](series-downsample-fl.md)|정수 요소를 기준으로 시계열을 다운 샘플링 합니다. |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|계열에 기본 지 수 다듬기 필터를 적용 합니다. |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|LOWESS 메서드를 사용 하 여 계열에 대 한 로컬 다항식을 맞춥니다. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|회귀 분석을 사용 하 여 계열에 다항식을 맞춥니다. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|계열에 이동 평균 필터를 적용 합니다. |
|[series_rolling_fl()](series-rolling-fl.md)|계열에 롤링 집계 함수를 적용 합니다. |
