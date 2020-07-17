---
title: 파일 포함
description: 포함 파일
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: e6704592a5b0f1a984ea5651eb8073ac105fda3d
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423248"
---
수집 및 쿼리 사이에 짧은 대기 시간이 필요한 경우 스트리밍 수집을 사용 하 여 데이터를 로드 합니다. 스트리밍 수집 작업은 10 초 이내에 완료 되며, 완료 된 후에는 데이터를 쿼리에 즉시 사용할 수 있습니다. 이 수집 방법은 수천 개의 테이블에 분산 된 초당 수천 개의 레코드와 같이 많은 양의 데이터를 수집 하는 데 적합 합니다. 각 테이블은 초당 몇 개의 레코드와 같이 비교적 적은 양의 데이터를 받습니다.

데이터 수집 크기가 테이블당 4gb를 초과 하는 경우 스트리밍 수집 대신 대량 수집을 사용 합니다.

다른 수집 방법에 대해 자세히 알아보려면 데이터 수집 [개요](../ingest-data-overview.md)를 참조 하세요.
