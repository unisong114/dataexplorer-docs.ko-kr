---
title: UI 딥 링크-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 UI 딥 링크에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: aa47811bfe8004037cb04e642c234003087617a1
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863237"
---
# <a name="ui-deep-links"></a>UI 딥 링크

REST API는 HTTP `GET` 요청에서 호출자를 UI 도구로 리디렉션하는 데 사용할 수 있는 심층 링크 기능을 제공 합니다. 예를 들어, Kusto. 탐색기 도구를 열고, 특정 클러스터와 데이터베이스에 대해 자동으로 구성 하 고, 특정 쿼리를 실행 하 고, 그 결과를 사용자에 게 표시 하는 URI를 만들 수 있습니다.

UI 딥 링크 REST API:

* 클러스터 (필수)는 일반적으로 REST API를 구현 하는 서비스로 암시적으로 정의 되지만 URI 쿼리 매개 변수를 지정 하 여 재정의할 수 있습니다 `uri` .

* 데이터베이스 (선택 사항)는 URI 경로의 첫 번째 및 유일한 조각으로 지정 됩니다. 데이터베이스는 쿼리에 대해 필수 이며 제어 명령의 경우 선택 사항입니다.

* 쿼리 또는 제어 명령 (옵션)은 URI 쿼리 매개 변수를 사용 하 여 지정 `query` 하거나 `querysrc` 쿼리를 보유 하는 웹 리소스를 가리키는 uri 쿼리 매개 변수를 사용 하 여 지정 합니다.
  `query`쿼리 또는 제어 명령 자체의 텍스트에서 사용할 수 있습니다 (HTTP 쿼리 매개 변수 인코딩을 사용 하 여 인코딩). 또는 쿼리 또는 제어 명령 텍스트에서 gzip의 base64 인코딩으로 사용할 수 있습니다. 긴 쿼리를 압축 하 여 기본 브라우저 URI 길이 제한에 맞게 설정할 수 있습니다.

* 클러스터 연결의 이름 (선택 사항)은 URI 쿼리 매개 변수를 사용 하 여 지정 합니다 `name` .

* UI 도구는 `web` 선택적 URI 쿼리 매개 변수를 사용 하 여 지정 됩니다.
  `web=0`데스크톱 응용 프로그램을 표시 합니다. 탐색기. `web=1`Kusto. WebExplorer 웹 응용 프로그램을 나타냅니다.
  `web=2`는 이전 버전의 Kusto. WebExplorer (Application Insights 분석 기반)입니다. `web=3`빈 프로필을 사용 하는 Kusto. WebExplorer입니다 (이전에 열린 탭 또는 클러스터는 사용할 수 없음). 마지막으로, `web` 쿼리 매개 변수를로 대체 `saw=1` 하 여 Kusto 탐색기의 톱 버전을 나타낼 수 있습니다.

링크에 대 한 몇 가지 예제는 다음과 같습니다.

* `https://help.kusto.windows.net/`: 사용자 에이전트 (예: 브라우저)가 요청을 실행 하면 `GET /` 클러스터를 쿼리하도록 구성 된 기본 UI 도구로 리디렉션됩니다. `help`
* `https://help.kusto.windows.net/Samples`: 사용자 에이전트 (예: 브라우저)가 요청을 실행 하면 `GET /Samples` 클러스터 데이터베이스를 쿼리하도록 구성 된 기본 UI 도구로 리디렉션됩니다 `help` `Samples` .
* `http://help.kusto.windows.net/Samples?query=StormEvents`: 사용자 (예: 브라우저)가 요청을 실행 하면 `GET /Samples?query=StormEvents` 클러스터 데이터베이스를 쿼리하도록 구성 된 기본 UI 도구로 리디렉션되고 쿼리를 실행 합니다 `help` `Samples` `StormEvents` .

> [!NOTE]
> 리디렉션에 사용 되는 UI 도구에서 인증을 수행 하므로 딥 링크 Uri에는 인증이 필요 하지 않습니다.
> 모든 `Authorization` HTTP 헤더 (제공 된 경우)는 무시 됩니다.

> [!IMPORTANT]
> 보안을 위해 `query` 또는 `querysrc` 가 딥 링크에 지정 된 경우에도 UI 도구는 제어 명령을 자동으로 실행 하지 않습니다.

## <a name="deep-linking-to-kustoexplorer"></a>Kusto 탐색기에 대 한 딥 링크

이 REST API는 특정 Kusto engine 클러스터에 대 한 연결을 열고 해당 클러스터에 대해 쿼리를 실행 하는 특수 하 게 작성 된 시작 매개 변수를 사용 하 여 Kusto 탐색기 데스크톱 클라이언트 도구를 설치 하 고 실행 하는 리디렉션을 수행 합니다.

* 경로: `/` [*DatabaseName*']
* 동사`GET`
* 쿼리 문자열:`web=0`

> [!NOTE]
> Kusto 탐색기를 시작 하는 리디렉션 URI 구문에 대 한 설명은 [Kusto. 탐색기를 사용 하 여 딥 링크](../../tools/kusto-explorer-using.md#deep-linking-queries) 를 참조 하세요.

## <a name="deep-linking-to-kustowebexplorer"></a>Kusto. WebExplorer에 대 한 딥 링크

이 REST API은 웹 응용 프로그램 인 Kusto. WebExplorer로의 리디렉션을 수행 합니다.

* 경로: `/` [*DatabaseName*']
* 동사`GET`
* 쿼리 문자열:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>URI에서 쿼리 또는 제어 명령 지정

URI 쿼리 문자열 매개 변수를 지정 하는 경우 `query` uri 쿼리 문자열 인코딩 HTML 규칙에 따라 인코딩해야 합니다. 또는 쿼리 또는 제어 명령의 텍스트를 gzip으로 압축 한 다음 base64 인코딩을 통해 인코딩할 수 있습니다. 이를 통해 더 긴 쿼리 또는 제어 명령을 보낼 수 있습니다 (후자의 인코딩 방법으로 인해 더 짧은 Uri를 생성 하므로).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>간접 참조로 쿼리 또는 제어 명령 지정

쿼리 또는 제어 명령이 매우 긴 경우 gzip/base64를 사용 하 여 인코딩하는 경우에도 사용자 에이전트의 최대 URI 길이를 초과 합니다. 또는 URI 쿼리 문자열 매개 변수가 제공 되며, `querysrc` 해당 값은 쿼리 또는 제어 명령 텍스트를 포함 하는 웹 리소스를 가리키는 짧은 URI입니다.

예를 들어 Azure Blob Storage에서 호스트 되는 파일에 대 한 URI가 될 수 있습니다.

> [!NOTE]
> 딥 링크가 웹 응용 프로그램 UI 도구에 있는 경우 쿼리 또는 제어 명령을 제공 하는 웹 서비스 (즉, URI를 제공 하는 서비스 `querysrc` )가에 대 한 CORS를 지원 하도록 구성 되어야 합니다 `dataexplorer.azure.com` .
>
> 또한 해당 서비스에서 인증/권한 부여 정보를 요구 하는 경우 URI 자체의 일부로 제공 해야 합니다.
>
> 예를 들어 `querysrc` Azure Blob Storage의 blob에서가 가리키는 경우 CORS를 지원 하도록 저장소 계정을 구성 하 고 blob 자체를 공개 (보안 클레임 없이 다운로드 가능) 하거나 URI에 적절 한 AZURE STORAGE SAS를 추가 해야 합니다. CORS 구성은 [Azure Portal](https://portal.azure.com/) 또는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)에서 수행할 수 있습니다.
> [Azure Storage의 CORS 지원을](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)참조 하세요.

