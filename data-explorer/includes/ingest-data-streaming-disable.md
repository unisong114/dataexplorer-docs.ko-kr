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
ms.openlocfilehash: a748332c85839bac8466532ba3959810a6387834
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423214"
---
## <a name="disable-streaming-ingestion-on-your-cluster"></a>클러스터에서 스트리밍 수집 사용 안 함

> [!WARNING]
> 스트리밍 수집을 사용 하지 않도록 설정 하는 데 몇 시간이 걸릴 수 있습니다.

Azure 데이터 탐색기 클러스터에서 스트리밍 수집을 사용 하지 않도록 설정 하기 전에 모든 관련 테이블 및 데이터베이스에서 [스트리밍 수집 정책을](../kusto/management/streamingingestionpolicy.md) 삭제 합니다. 스트리밍 수집 정책을 제거 하면 Azure 데이터 탐색기 클러스터 내에서 재배치 되는 데이터가 트리거됩니다. 스트리밍 수집 데이터는 초기 저장소에서 열 저장소 (익스텐트 또는 분할)의 영구 저장소로 이동 됩니다. 이 프로세스는 초기 저장소에 있는 데이터의 양에 따라 몇 초에서 몇 시간이 걸릴 수 있습니다.
