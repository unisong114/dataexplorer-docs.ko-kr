---
title: Azure Data Explorer REST API - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 Azure Data Explorer REST API에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6abfe15490e5c633e1cb7912f4794ee90bec1947
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373595"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer REST API

이 문서에서는 HTTPS를 통해 Kusto를 조작하는 방법에 대해 설명합니다.

## <a name="supported-actions"></a>지원되는 작업

엔드포인트가 지원하는 작업 목록은 엔드포인트가 엔진 엔드포인트인지 또는 데이터 관리 엔드포인트인지에 따라 다릅니다.

|작업         |HTTP 동사   |URI 템플릿           |엔진|데이터 관리|인증 |
|---------------|------------|-----------------------|------|---------------|---------------|
|쿼리          |GET 또는 POST |/v1/rest/query         |예   |예             |예            |
|쿼리          |GET 또는 POST |/v2/rest/query         |예   |예             |예            |
|관리     |POST        |/v1/rest/mgmt          |예   |예            |예            |
|UI             |GET         |/                      |예   |예             |예             |
|UI             |GET         |/{dbname}              |예   |예             |예             |

여기서 *작업*은 관련 작업 그룹을 나타냅니다.

* 쿼리 작업은 쿼리를 서비스로 보내고 쿼리 결과를 가져옵니다.
* 관리 작업은 제어 명령을 서비스로 보내고 제어 명령의 결과를 다시 가져옵니다.
* UI 작업을 통해 데스크톱 클라이언트 또는 웹 클라이언트를 시작할 수 있습니다. 작업은 HTTP 리디렉션 응답을 통해 수행되어 서비스와 상호 작용합니다.

## <a name="next-steps"></a>다음 단계

쿼리 및 관리 작업의 HTTP 요청 및 응답에 대한 자세한 내용은 다음을 참조하세요.
 * [쿼리 관리 HTTP 요청](./request.md)
 * [쿼리 관리 HTTP 응답](./response.md)
 * [쿼리 v2 HTTP 응답](./response2.md)

UI 작업에 대한 자세한 내용은 다음을 참조하세요.
 * [UI 딥 링크](./deeplink.md)
