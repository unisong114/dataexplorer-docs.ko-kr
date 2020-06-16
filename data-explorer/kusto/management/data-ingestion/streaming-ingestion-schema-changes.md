---
title: 스트리밍 수집 및 스키마 변경-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 스트리밍 수집을 사용 하 여 스키마 변경을 처리 하는 옵션을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: f1ab3d012be7751eba33447b325748859509af00
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784568"
---
# <a name="streaming-ingestion-and-schema-changes"></a>스트리밍 수집 및 스키마 변경

## <a name="background"></a>배경

클러스터 노드는 스트리밍 수집을 통해 데이터를 수신 하는 데이터베이스의 스키마를 캐시 합니다. 이 프로세스는 클러스터 리소스의 성능과 사용률을 최적화 하지만 스키마가 변경 되 면 전파가 지연 될 수 있습니다.

스키마 변경의 예는 다음과 같습니다.

* 데이터베이스 및 테이블 만들기 및 삭제
* 테이블의 열 추가, 제거, 다시 입력 또는 이름 바꾸기
* 미리 만든 수집 매핑 추가 또는 제거
* 정책 추가, 제거 또는 변경

스키마 변경 및 스트리밍 수집 흐름이 조정 되지 않은 면 일부 스트리밍 수집 요청이 실패할 수 있습니다. 오류에는 스키마 관련 오류가 포함 되거나 불완전 하거나 왜곡 된 데이터를 테이블에 삽입할 수 있습니다.
사용자 지정 수집 응용 프로그램을 구현 하는 경우 제한 된 시간 동안 다시 시도를 수행 하거나 대기 중인 수집 방법을 통해 실패 한 요청에서 데이터를 다시 배치 하 여 스키마 관련 오류를 처리 하는 것이 좋습니다.

## <a name="clearing-the-schema-cache"></a>스키마 캐시 지우기

클러스터 노드에서 스키마 캐시를 명시적으로 지워 전파 지연의 영향을 줄입니다.
스트리밍 수집 관리 명령 [에 대해 clear schema cache](clear-schema-cache-command.md) 중 하나를 사용 하 여 스키마 캐시를 지웁니다.
스트리밍 수집 흐름과 스키마 변경을 조정 하면 오류 및 연결 된 데이터 왜곡을 완전히 제거할 수 있습니다. 

**조정 흐름 예제:**

1. 스트리밍 수집을 일시 중단 합니다.
1. 처리 중인 모든 스트리밍 수집 요청이 완료 될 때까지 기다립니다>
1. 스키마를 변경 합니다.
1. 하나 또는 여러 `.clear cache streaming ingestion` 스키마 명령을 실행 합니다. 
    * 성공할 때까지 반복 하 고 명령 출력의 모든 행이 success를 나타냄
1. 스트리밍 수집을 다시 시작 합니다.

> [!NOTE]
> 캐시 지우기 스트리밍 수집 스키마 명령을 자주 사용 하면 스트리밍 수집의 성능에 부정적인 영향을 줄 수 있습니다.
