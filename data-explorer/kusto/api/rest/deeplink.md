---
title: UI 딥 링크 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 UI 심층 링크를 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: c9535ced274ccdbe38f8d9a765e98d11e7b381e5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523684"
---
# <a name="ui-deep-links"></a>UI 딥 링크

REST API는 HTTP `GET` 요청이 호출자를 UI 도구로 리디렉션할 수 있는 딥 링크 기능을 제공합니다. 예를 들어 Kusto.Explorer 도구를 열고, 특정 클러스터 및 데이터베이스에 대해 자동으로 구성하고, 특정 쿼리를 실행하고, 결과를 사용자에게 표시하는 URI를 만들 수 있습니다.

UI 딥 링크 REST API:

* 클러스터(필수)는 일반적으로 REST API를 구현하는 서비스로서 암시적으로 정의되지만 URI 쿼리 매개 변수를 `uri`지정하여 재정의할 수 있습니다.

* 데이터베이스(선택 사항)는 URI 경로의 첫 번째 및 유일한 조각으로 지정됩니다. 데이터베이스는 쿼리에 필수이며 제어 명령에는 선택 사항입니다.

* 쿼리 또는 제어 명령(선택 사항)은 URI `query`쿼리 매개 변수 `querysrc` 또는 URI 쿼리 매개 변수(쿼리를 보유하는 웹 리소스를 가리키는)를 사용하여 지정됩니다.
  `query`HTTP 쿼리 매개 변수 인코딩을 사용하여 인코딩된 쿼리 또는 제어 명령 자체의 텍스트에 사용할 수 있습니다. 또는 쿼리 또는 제어 명령 텍스트의 gzip의 base64 인코딩에 사용할 수 있습니다(기본 브라우저 URI 길이 제한에 맞게 긴 쿼리를 압축할 수 있음).

* 클러스터 연결의 이름(선택 사항)은 URI 쿼리 `name`매개 변수를 사용하여 지정됩니다.

* UI 도구는 선택적 URI `web` 쿼리 매개 변수를 사용하여 지정됩니다.
  `web=0`은 데스크톱 응용 프로그램 Kusto.Explorer를 나타냅니다. `web=1`은 Kusto.WebExplorer 웹 응용 프로그램을 나타냅니다.
  `web=2`는 Kusto.WebExplorer의 이전 버전입니다(응용 프로그램 인사이트 분석 기반). `web=3`는 Kusto.WebExplorer에서 빈 프로필이 있습니다(이전에 열려 있는 탭이나 클러스터를 사용할 수 없음). 마지막으로 Kusto.Explorer의 `web` `saw=1` SAW 버전을 나타내기 위해 쿼리 매개 변수를 대체할 수 있습니다.

링크에 대한 몇 가지 예는 다음과 같습니다.

* `https://help.kusto.windows.net/`: 사용자 에이전트(예: 브라우저)가 `GET /` 요청을 발행하면 `help` 클러스터를 쿼리하도록 구성된 기본 UI 도구로 리디렉션됩니다.
* `https://help.kusto.windows.net/Samples`: 사용자 에이전트(예: 브라우저)가 `GET /Samples` 요청을 발행하면 `help` 클러스터 `Samples` 데이터베이스를 쿼리하도록 구성된 기본 UI 도구로 리디렉션됩니다.
* `http://help.kusto.windows.net/Samples?query=StormEvents`: 사용자(예: 브라우저)가 `GET /Samples?query=StormEvents` 요청을 발행하면 `help` 클러스터 `Samples` 데이터베이스를 쿼리하고 `StormEvents` 쿼리를 수행하도록 구성된 기본 UI 도구로 리디렉션됩니다.

> [!NOTE]
> 딥 링크 URI는 리디렉션에 사용되는 UI 도구에 의해 인증이 수행되므로 인증이 필요하지 않습니다.
> 제공된 `Authorization` 모든 HTTP 헤더는 무시됩니다.

> [!IMPORTANT]
> 보안상의 이유로 UI 도구는 딥 링크에 `query` 지정되거나 `querysrc` 지정된 경우에도 제어 명령을 자동으로 실행하지 않습니다.

## <a name="deep-linking-to-kustoexplorer"></a>쿠스토에 대한 깊은 연결.탐색기

이 REST API는 특정 Kusto 엔진 클러스터에 대한 연결을 열고 해당 클러스터에 대한 쿼리를 실행하는 특수 하게 제작된 시작 매개 변수를 사용하여 Kusto.Explorer 데스크톱 클라이언트 도구를 설치하고 실행하는 리디렉션을 수행합니다.

* 경로: `/` *[데이터베이스 이름*']
* 동사:`GET`
* 쿼리 문자열:`web=0`

> [!NOTE]
> Kusto.Explorer를 시작하기 위한 리디렉션 URI 구문에 대한 설명은 [Kusto.Explorer와의 딥 링크를](../../tools/kusto-explorer.md#deep-linking-queries) 참조하십시오.

## <a name="deep-linking-to-kustowebexplorer"></a>쿠스토에 대한 깊은 연결.웹익탐색

이 REST API는 웹 응용 프로그램인 Kusto.WebExplorer로 리디렉션을 수행합니다.

* 경로: `/` *[데이터베이스 이름*']
* 동사:`GET`
* 쿼리 문자열:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>URI에서 쿼리 또는 제어 명령 지정

URI 쿼리 문자열 `query` 매개 변수를 지정하는 경우 HTML 규칙을 인코딩하는 URI 쿼리 문자열에 따라 인코딩해야 합니다. 또는 쿼리 또는 제어 명령의 텍스트를 gzip으로 압축한 다음 base64 인코딩을 통해 인코딩할 수 있습니다. 이렇게 하면 더 긴 쿼리 또는 제어 명령을 보낼 수 있습니다(후자의 인코딩 메서드는 더 짧은 URI를 생성하기 때문에).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>간접으로 쿼리 또는 제어 명령 지정

쿼리 또는 제어 명령이 매우 긴 경우 gzip/base64를 사용하여 인코딩하는 경우에도 사용자 에이전트의 최대 URI 길이를 초과합니다. 또는 URI 쿼리 문자열 `querysrc` 매개 변수가 제공되며 해당 값은 쿼리 또는 제어 명령 텍스트를 보유하는 웹 리소스를 가리키는 짧은 URI입니다.

예를 들어 Azure Blob Storage에서 호스팅하는 파일에 대한 URI일 수 있습니다.

> [!NOTE]
> 웹 응용 프로그램 UI 도구에 대한 딥 링크인 경우 쿼리 또는 제어 명령(즉, URI를 `querysrc` 제공하는 서비스)을 `dataexplorer.azure.com`제공하는 웹 서비스는 에 대한 CORS를 지원하도록 구성되어야 합니다.
>
> 또한 해당 서비스에 의해 인증/권한 부여 정보가 필요한 경우 URI 자체의 일부로 제공되어야 합니다.
>
> 예를 들어 `querysrc` Azure Blob Storage의 Blob을 가리키는 경우 CORS를 지원하도록 저장소 계정을 구성하고 Blob 자체를 공개(보안 클레임 없이 다운로드할 수 있음) URI에 적절한 Azure 저장소 SAS를 추가해야 합니다. CORS 구성은 Azure [포털](https://portal.azure.com/) 또는 [Azure 저장소 탐색기에서](https://azure.microsoft.com/features/storage-explorer/)수행할 수 있습니다.
> [Azure 저장소의 CORS 지원을](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)참조하십시오.

