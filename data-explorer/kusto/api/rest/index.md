---
title: Azure Data Explorer REST API - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 Azure Data Explorer REST API에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 000561f96bd4174b94cca8e9db4c932a3cf3f0c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490687"
---
# <a name="azure-data-explorer-rest-api"></a>Azure Data Explorer REST API

이 문서에서는 HTTPS를 통해 Kusto를 조작하는 방법에 대해 설명합니다.

## <a name="what-actions-are-supported"></a>어떤 작업이 지원되나요?

엔드포인트가 지원하는 작업 목록은 엔드포인트가 엔진 엔드포인트인지 또는 데이터 관리 엔드포인트인지에 따라 다릅니다.

|작업         |HTTP 동사  |URI 템플릿             |엔진|데이터 관리|인증 여부|
|---------------|-----------|-------------------------|------|---------------|---------------|
|쿼리          |GET 또는 POST|`/v1/rest/query`         |예   |예             |예            |
|쿼리          |GET 또는 POST|`/v2/rest/query`         |예   |예             |예            |
|관리     |POST       |`/v1/rest/mgmt`          |예   |예            |예            |
|UI             |GET        |`/`                      |예   |예             |예             |
|UI             |GET        |`/{dbname}`              |예   |예             |예             |

여기서 **작업**은 관련 작업 그룹을 나타냅니다.

* **쿼리** 작업은 쿼리를 서비스로 보내고 쿼리 결과를 가져옵니다.
* **관리** 작업은 제어 명령을 서비스로 보내고 제어 명령의 결과를 다시 가져옵니다.
* **UI** 작업은 서비스와 상호 작용하기 위해 데스크톱 클라이언트 또는 웹 클라이언트(HTTP 리디렉션 응답을 통해)를 시작하는 데 사용할 수 있습니다.

쿼리 및 관리 작업의 HTTP 요청/응답에 대한 세부 정보는 [쿼리/관리 HTTP 요청](./request.md), [쿼리/관리 HTTP 응답](./response.md) 및 [쿼리 v2 HTTP 응답](./response2.md)을 참조하세요. Ui 작업에 대한 자세한 내용은 [UI(딥 링크)](./deeplink.md)를 참조하세요.