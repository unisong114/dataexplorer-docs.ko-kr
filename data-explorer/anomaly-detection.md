---
title: Azure 데이터 탐색기에서 & 이상 검색
description: Azure 데이터 탐색기를 사용하여 이상 검색 및 예측을 위해 열렬 데이터를 분석하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: eac1237e098b90f526e441ef89f0902f72abde53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496707"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Azure 데이터 탐색기의 이상 징후 검색 및 예측

Azure Data Explorer는 클라우드 서비스 또는 IoT 장치에서 원격 분석 데이터의 진행 중수집을 수행합니다. 이 데이터는 서비스 상태 모니터링, 물리적 생산 프로세스, 사용 추세 및 부하 예측과 같은 다양한 통찰력을 위해 분석됩니다. 분석은 선택한 메트릭의 시간계에서 수행되어 일반적인 일반 기준 패턴을 기준으로 메트릭의 편차 패턴을 찾습니다. Azure Data Explorer에는 여러 열계의 생성, 조작 및 분석에 대한 기본 지원이 포함되어 있습니다. 수천 개의 서계열을 몇 초 만에 생성및 분석할 수 있어 거의 실시간으로 모니터링솔루션과 워크플로우를 구현할 수 있습니다.

이 문서에서는 Azure 데이터 탐색기 열계 변칙 검색 및 예측 기능에 대해 자세히 설명합니다. 적용 가능한 시계열 함수는 각 원래 시계열이 계절, 추세 및 잔류 구성 요소로 분해되는 견고한 잘 알려진 분해 모델을 기반으로 합니다. 이상 징후는 잔류 구성 요소의 이상값에 의해 감지되고 예측은 계절 및 추세 구성 요소를 추정하여 수행됩니다. Azure Data Explorer 구현은 자동 계절성 검색, 강력한 이상값 분석 및 벡터화된 구현을 통해 기본 분해 모델을 크게 향상시켜 수천 개의 시간계를 몇 초 만에 처리합니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure 데이터 탐색기에서 열렬 분석을](/azure/data-explorer/time-series-analysis) 읽고 열렬 기능에 대한 개요를 확인합니다.

## <a name="time-series-decomposition-model"></a>타임시리즈 분해 모델

열렬 예측 및 변칙 검색을 위한 Azure Data Explorer 네이티브 구현은 잘 알려진 분해 모델을 사용합니다. 이 모델은 서비스 트래픽, 구성 요소 하트비트 및 IoT 주기적인 측정과 같은 주기적 및 추세 동작을 나타낼 것으로 예상되는 메트릭의 타임시리즈에 적용되어 향후 메트릭 값을 예측하고 비정상적인 메트릭값을 감지합니다. 이 회귀 프로세스의 가정은 이전에 알려진 계절 및 추세 동작 이외에, 타임계가 무작위로 분포된다는 것입니다. 그런 다음 계절 및 추세 구성 요소(총칭하는 기준선)에서 미래 메트릭 값을 예측하고 잔여 부분을 무시할 수 있습니다. 잔류 부분만 사용하여 이상값 분석을 기반으로 비정상적인 값을 검색할 수도 있습니다.
분해 모델을 만들려면 이 함수를 [`series_decompose()`](kusto/query/series-decomposefunction.md)사용합니다. 이 `series_decompose()` 함수는 일련의 시계열을 가져와 각 시계열을 계절, 추세, 잔류 및 기준 구성 요소로 자동으로 분해합니다. 

예를 들어 다음 쿼리를 사용하여 내부 웹 서비스의 트래픽을 분해할 수 있습니다.

**\[**[**쿼리를 실행하려면 클릭합니다.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![시간 시계열 분해](media/anomaly-detection/series-decompose-timechart.png)

* 원래 시간열에는 num(빨간색)으로 레이블이 지정됩니다. **num** 
* 이 프로세스는 함수를 [`series_periods_detect()`](kusto/query/series-periods-detectfunction.md) 사용하여 계절성을 자동으로 감지하여 시작하고 **계절** 패턴(보라색)을 추출합니다.
* 계절 패턴은 원래 시계열에서 빼고 선형 회귀는 **추세** 구성 [`series_fit_line()`](kusto/query/series-fit-linefunction.md) 요소를 찾기 위해 함수를 사용하여 실행됩니다(연한 파란색).
* 함수는 추세를 빼고 나머지는 **잔류** 구성 요소(녹색)입니다.
* 마지막으로 이 함수는 계절 및 추세 구성 요소를 추가하여 **기준선을** 생성합니다(파란색).

## <a name="time-series-anomaly-detection"></a>시계열 변칙 검색

이 [`series_decompose_anomalies()`](kusto/query/series-decompose-anomaliesfunction.md) 함수는 시계열 세트에서 비정상적인 점을 찾습니다. 이 함수는 분해 모델을 빌드하기 위해 호출한 `series_decompose()` 다음 잔류 구성 요소에서 실행됩니다. [`series_outliers()`](kusto/query/series-outliersfunction.md) `series_outliers()`Tukey의 울타리 테스트를 사용하여 잔류 구성 요소의 각 점에 대한 변칙 점수를 계산합니다. 1.5 이하 또는 -1.5 이하의 이상 점수는 각각 온화한 변칙 상승 또는 하락을 나타냅니다. 3.0 이하또는 -3.0 이하의 변칙 점수는 강력한 변칙을 나타냅니다. 

다음 쿼리를 사용하면 내부 웹 서비스 트래픽에서 이상을 검색할 수 있습니다.

**\[**[**쿼리를 실행하려면 클릭합니다.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![시계열 변칙 검색](media/anomaly-detection/series-anomaly-detection.png)

* 원래 시간열(빨간색)입니다. 
* 기준선(계절 + 추세) 구성 요소(파란색)입니다.
* 원래 시간열 위에 있는 비정상적인 점(보라색)입니다. 비정상적인 점은 예상기준값에서 크게 벗어나는 것입니다.

## <a name="time-series-forecasting"></a>타임시리즈 예측

이 [`series_decompose_forecast()`](kusto/query/series-decompose-forecastfunction.md) 함수는 시계열 집합의 미래 값을 예측합니다. 이 함수는 분해 모델을 빌드하기 위해 호출한 `series_decompose()` 다음 각 시계열에 대해 기준 구성 요소를 향후로 추정합니다.

다음 쿼리를 사용하면 다음 주의 웹 서비스 트래픽을 예측할 수 있습니다.

**\[**[**쿼리를 실행하려면 클릭합니다.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![타임시리즈 예측](media/anomaly-detection/series-forecasting.png)

* 원래 메트릭(빨간색)입니다. 미래 값이 누락되어 기본적으로 0으로 설정됩니다.
* 다음 주 값을 예측하기 위해 기준 구성요소(파란색)를 추정합니다.

## <a name="scalability"></a>확장성

Azure Data Explorer 쿼리 언어 구문을 사용하면 단일 호출로 여러 열계를 처리할 수 있습니다. 고유한 최적화된 구현을 통해 빠른 성능을 구현할 수 있으며, 이는 거의 실시간 시나리오에서 수천 개의 카운터를 모니터링할 때 효과적인 변칙 검색 및 예측에 매우 중요합니다.

다음 쿼리는 세 개의 시간계를 동시에 처리하는 것을 보여 주며,

**\[**[**쿼리를 실행하려면 클릭합니다.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![타임시리즈 확장성](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>요약

이 문서에서는 시계열 변칙 검색 및 예측에 대한 네이티브 Azure Data Explorer 함수에 대해 자세히 설명합니다. 각 원래 시간계는 이상 징후 및/또는 예측을 감지하기 위해 계절적, 추세 및 잔류 구성 요소로 분해됩니다. 이러한 기능은 오류 감지, 예측 유지 관리, 수요 및 부하 예측과 같은 거의 실시간 모니터링 시나리오에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 데이터 탐색기에서 [기계 학습 기능에](/azure/data-explorer/machine-learning-clustering) 대해 알아봅니다.
