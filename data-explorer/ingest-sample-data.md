---
title: Azure 데이터 탐색기로 샘플 데이터 수집
description: Azure 데이터 탐색기로 날씨 관련 샘플 데이터를 수집(로드)하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.localizationpriority: high
ms.openlocfilehash: d5cff511a67e122af6b71740bbeaec6b7a3048e4
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512760"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>빠른 시작: Azure 데이터 탐색기로 샘플 데이터 수집

이 문서에서는 Azure 데이터 탐색기로 샘플 데이터를 수집(로드)하는 방법을 설명합니다. [데이터는 여러 가지 방식으로 수집할 수 있습니다](ingest-data-overview.md). 이 문서에서는 테스트용으로 적합한 기본적인 방식을 중점적으로 설명합니다.

> [!NOTE]
> [Azure Data Explorer Python 라이브러리를 사용하여 데이터 수집](python-ingest-data.md)을 완료한 경우 이 데이터는 이미 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

## <a name="ingest-data"></a>데이터 수집

**StormEvents** 샘플 데이터 집합에는 [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/)에서 제공하는 날씨 관련 데이터가 포함되어 있습니다.

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com)에 로그인합니다.

1. 애플리케이션의 왼쪽 위에서 **클러스터 추가** 를 선택합니다.

1. **클러스터 추가** 대화 상자에 `https://<ClusterName>.<Region>.kusto.windows.net/` 형식으로 클러스터 URL을 입력하고 **추가** 를 선택합니다.

1. 다음 명령을 붙여넣고 **실행** 을 선택하여 StormEvents 테이블을 만듭니다.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. 다음 명령을 붙여넣고 **실행** 을 선택하여 StormEvents 테이블에 데이터를 수집합니다.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. 수집이 완료되면 다음 쿼리를 붙여넣고 창에서 쿼리를 선택한 다음 **실행** 을 선택합니다.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    쿼리가 수집된 샘플 데이터에서 다음 결과를 반환합니다.

    ![쿼리 결과](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer 데이터 수집](ingest-data-overview.md)에서 수집 방법에 대해 자세히 알아봅니다.
* [빠른 시작: Azure Data Explorer Web UI에서 데이터 쿼리](web-query-data.md)를 완료합니다.
* Kusto 쿼리 언어를 사용하여 [쿼리를 작성](write-queries.md)합니다.
