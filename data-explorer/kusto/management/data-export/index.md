---
title: 데이터 내보내기 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 데이터 내보내기에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e6afcf86a02f1f74fe024c1b94d7f9458a72a4e7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763431"
---
# <a name="data-export"></a>데이터 내보내기

데이터 내보내기는 Kusto 쿼리를 실행하고 결과를 기록하는 프로세스입니다. 이후 검사에 쿼리 결과를 사용할 수 있습니다.

데이터를 내보내는 몇 가지 방법은 다음과 같습니다.

## <a name="client-side-export"></a>클라이언트 쪽 내보내기
  가장 간단한 형태의 데이터 내보내기는 클라이언트 쪽에서 수행할 수 있습니다. 클라이언트는 서비스에 대해 쿼리를 실행하고 결과를 다시 읽은 다음, 이를 작성합니다.
이 형태의 데이터 내보내기는 일반적으로 도구가 실행되는 로컬 파일 시스템으로 내보내는 클라이언트 도구에 따라 달라집니다. 이 모델을 지원하는 도구 중에는 [Kusto.Explorer](../../tools/kusto-explorer.md) 및 [웹 UI](../../../web-query-data.md)가 있습니다.

## <a name="service-side-export-pull"></a>서비스 쪽 내보내기(끌어오기)
  내보내기 대상이 쿼리와 같거나 다른 클러스터/데이터베이스에 있는 테이블인 경우 대상 테이블에서 "쿼리에서 수집"을 사용합니다. 이 흐름에서는 쿼리가 실행되고, 해당 결과가 테이블에 즉시 수집됩니다. 자세한 내용은 [쿼리에서 수집](../../management/data-ingestion/ingest-from-query.md)을 참조하세요.

## <a name="service-side-export-push"></a>서비스 쪽 내보내기(밀어넣기)
  위 방법과 [클라이언트 쪽 내보내기](#client-side-export) 및 [서비스 쪽 내보내기(끌어오기)](#service-side-export-pull)는 제한됩니다. 쿼리 결과는 쿼리를 수행하는 생산자와 결과를 작성하는 소비자 간에 단일 네트워크 연결을 통해 스트리밍되어야 합니다.
확장 가능한 데이터 내보내기를 위해 쿼리를 실행하는 서비스도 해당 결과를 최적화된 방식으로 기록하는 "밀어넣기" 내보내기 모델을 사용합니다. 이 모델은 `.export` 제어 명령 세트를 통해 공개되며, 쿼리 결과를 [외부 테이블](export-data-to-an-external-table.md), [SQL 테이블](export-data-to-sql.md) 또는 [외부 Blob 스토리지](export-data-to-storage.md)로 내보낼 수 있도록 지원합니다.
  
  서비스 쪽 내보내기 명령은 클러스터에서 사용 가능한 데이터 내보내기 용량으로 제한됩니다.
[show capacity(용량 표시) 명령](../../management/diagnostics.md#show-capacity)을 실행하여 클러스터의 전체, 사용 및 잔존 데이터 내보내기 용량을 확인할 수 있습니다.

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>데이터 내보내기 명령을 사용하는 경우 비밀 관리에 대한 추천 사항

Azure Blob Storage 및 Azure SQL Database와 같은 원격 대상으로 데이터를 내보내는 것이 가장 좋습니다. 데이터 내보내기 명령을 실행하는 보안 주체의 자격 증명을 암시적으로 사용합니다. 일부 시나리오에서는 이 방법을 사용할 수 없습니다. 예를 들어, Azure Blob Storage는 보안 주체 개념을 지원하지 않고 자체 토큰만 지원합니다.
이 기능은 데이터 내보내기 제어 명령의 일부로 필요한 자격 증명을 인라인으로 도입할 수 있도록 지원합니다.

안전한 방법으로 내보내려면 다음을 수행합니다.

* 비밀을 보낼 때 [난독 처리된 문자열 리터럴](../../query/scalar-data-types/string.md#obfuscated-string-literals)(예: `h@"..."`)을 사용합니다. 비밀은 내부적으로 발생한 흔적이 남지 않도록 삭제됩니다.

* 암호 및 이와 비슷한 비밀은 안전하게 저장하고, 필요에 따라 애플리케이션에서 "끌어와야" 합니다.
