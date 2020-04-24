---
title: 좁은 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 좁은 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75b211f32c15eefc60ca40b0408345be4a656652
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512243"
---
# <a name="narrow-plugin"></a>좁은 플러그인

```kusto
T | evaluate narrow()
```

플러그인은 `narrow` 행 번호, 열 유형 및 열 값(로)의 `string`세 개의 열만 있는 테이블로 와이드 테이블을 "피벗 해제"합니다.

플러그인은 `narrow` 주로 디스플레이 목적을 위해 설계, 그것은 넓은 테이블을 수평 스크롤의 필요 없이 편안 하 게 표시 할 수 있습니다.

**구문**

`T | evaluate narrow()`

**예**

다음 예제에서는 Kusto `.show diagnostics` 제어 명령의 출력을 쉽게 읽을 수 있는 방법을 보여 주십습니다.

```kusto
.show diagnostics
 | evaluate narrow()
```

그 자체의 `.show diagnostics` 결과는 단일 행과 33개의 열이 있는 테이블입니다. 플러그인을 `narrow` 사용 하 여 우리는 "회전" 다음과 같은 것 으로 출력:

행  | 열                              | 값
-----|-------------------------------------|-----------------------------
0    | IsHealthy                           | True
0    | 이스리밸런스데스                 | False
0    | IsScaleOut필수                  | False
0    | 기계 총                       | 2
0    | 기계 오프라인                     | 0
0    | 노드라스트리스타트온                 | 2017-03-14 10:59:18.9263023
0    | 관리자마지막선출                  | 2017-03-14 10:58:41.6741934
0    | 클러스터웜데이터용량팩터       | 0.130552847673333
0    | 익스텐트합계                        | 136
0    | 디스크콜드할당백분율        | 5
0    | 인스턴스대상기반데이터용량  | 2
0    | 토탈 오리지널 데이터크기               | 5167628070
0    | 총익 크기                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | 섭취진행중                | 0
0    | 섭취성공률               | 100
0    | 병합진행상황                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | 빌드 타임                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsData웜데필요한               | False
0    | 리밸런스라스트런온                  | 2017-03-21 09:14:53.8523455
0    | 데이터 워밍라스트런온                | 2017-03-21 09:19:54.1438800
0    | 병합성공률                   | 100
0    | NotHealthyReason                    | [null]
0    | IsAttention필수                 | False
0    | 주의필수이유             | [null]
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | 실패한 작업              | 0
0    | 실패한 병합작업               | 0
0    | 맥스익스익스인싱글테이블             | 64
0    | 테이블위드맥스익스                 | 쿠스토 모니터링지속데이터베이스.쿠스토모니터링테이블
0    | 웜익스텐트크기                      | 1779165230