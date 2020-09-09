---
title: 함수 라이브러리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기 기능을 확장 하는 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 66f8da1655fada7429fcd3087810904bd184546c
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614515"
---
# <a name="functions-library"></a>함수 라이브러리

다음 문서에는 사용자 정의 함수의 분류 된 목록이 포함 되어 있습니다.

## <a name="machine-learning-functions"></a>기계 학습 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[predict_lf ()](predict-lf.md)|기존의 학습 된 기계 학습 모델을 사용 하 여 예측 합니다. |
|[predict_onnx_lf ()](predict-onnx-lf.md)| ONNX 형식으로 기존의 학습 된 기계 학습 모델을 사용 하 여 예측 합니다. |

## <a name="series-processing-functions"></a>계열 처리 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_lf ()](quantize-lf.md)|양자화 metric 열. |
|[series_fit_poly_lf ()](series-fit-poly-lf.md)|회귀 분석을 사용 하 여 계열에 다항식을 맞춥니다. |
|[series_moving_avg_lf ()](series-moving-avg-lf.md)|계열에 이동 평균 필터를 적용 합니다. |
|[series_rolling_lf ()](series-rolling-lf.md)|계열에 롤링 집계 함수를 적용 합니다. |
