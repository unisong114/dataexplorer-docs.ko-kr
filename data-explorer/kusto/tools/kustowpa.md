---
title: 쿠스토 WPA - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto WPA에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 2d8c5a9b11d8045897d8a9bd798e40ceb0f48b6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503301"
---
# <a name="kusto-wpa"></a>쿠스토 WPA

Kusto WPA는 WPA(Windows 성능 분석기)에서 Kusto 쿼리 결과를 실행하고 시각화하는 기능을 추가합니다. 그것은 두 가지 주요 부분으로 구성되어 있습니다 :

1. KUsto.Explorer 공유 쿼리(via)를 `Share` &gt; `Query to Clipboard`수락하고 WPA에서 처리할 쿼리`.kpq`파일()을 생성할 수 있는 런처 도구입니다.

1. 생성된 쿼리 파일()을`.kpq`"열 수 있는" WPA 플러그인입니다. 이러한 파일을 열면 파일에 지정된 쿼리가 Kusto 클러스터로 자동으로 전송되고 "표준" ETL 파일에서 온 것처럼 결과를 로드합니다.

자세한https://aka.ms/kustowpa내용 및 다운로드 정보는 [] 를 참조하십시오.