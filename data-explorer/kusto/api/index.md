---
title: Azure Data Explorer API 개요 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 Azure Data Explorer API 개요에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: c791ae0be0c895a4744e761c58e7d455aa0a22b9
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610273"
---
# <a name="azure-data-explorer-api-overview"></a>Azure Data Explorer API 개요

Azure Data Explorer 서비스에서 지원하는 엔드포인트는 다음과 같습니다.

1. Azure Data Explorer에서 데이터를 쿼리하고 관리할 수 있는 [REST API](#rest-api) 엔드포인트입니다.
   이 엔드포인트는 쿼리 및 [제어 명령](../management/index.md)에 대한 [Kusto 쿼리 언어](../query/index.md)를 지원합니다.
2. Microsoft SQL Server 제품에서 사용하는 Microsoft TDS(Tabular Data Stream) 프로토콜의 하위 집합을 구현하는 [MS-TDS](#ms-tds) 엔드포인트입니다.
   이 엔드포인트는 주로 쿼리를 위해 SQL Server 엔드포인트와 통신하는 방법을 알고 있는 기존 도구에 유용합니다.
3. [ARM(Azure Resource Manager)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) 엔드포인트입니다. 이 엔드포인트는 Azure 서비스에서 Azure Data Explorer 클러스터와 같은 리소스를 관리하는 표준 수단입니다.

Azure Data Explorer는 프로그래밍 방식으로 쉽게 액세스 할 수 있도록 위의 엔드포인트를 사용하는 여러 클라이언트 라이브러리를 제공합니다.

1. .NET SDK
2. Python SDK
3. Java SDK
4. Node SDK
5. Go SDK
6. PowerShell
7. R

## <a name="rest-api"></a>REST API

Azure Data Explorer 서비스와 통신하는 기본적인 방법은 서비스의 REST API를 사용하는 것입니다. 완전히 문서화된 이 엔드포인트를 통해 호출자는 다음을 수행할 수 있습니다.

1. 쿼리 데이터
2. 메타데이터 쿼리 및 수정
3. 데이터 수집
4. 서비스 상태 쿼리
5. 리소스 관리

서로 다른 Azure Data Explorer 서비스는 공개적으로 사용할 수 있는 동일한 REST API를 사용하여 서로 통신합니다.

REST API를 사용하여 Azure Data Explorer에 대한 요청을 지원하는 것 외에도, REST API 프로토콜을 처리하지 않고 서비스를 사용할 수 있는 클라이언트 라이브러리가 많이 있습니다.

## <a name="ms-tds"></a>MS-TDS

Azure Data Explorer에 연결하여 해당 데이터를 쿼리하는 다른 방법으로, Azure Data Explorer는 Microsoft SQL Server 통신 프로토콜(MS-TDS)을 지원하며 T-SQL 쿼리 실행에 대한 제한된 지원을 포함합니다. 이를 통해 사용자는 잘 알려진 쿼리 구문(T-SQL)과 익숙한 데이터베이스 클라이언트 도구(예: LINQPad, sqlcmd, Tableau, Excel 및 Power BI)를 사용하여 Azure Data Explorer에서 쿼리를 실행할 수 있습니다.

MS-TDS에 대한 자세한 내용은 [이 페이지](tds/index.md)에서 확인할 수 있습니다.

## <a name="net-framework-libraries"></a>.NET Framework 라이브러리

.NET Framework 라이브러리는 Azure Data Explorer 기능을 프로그래밍 방식으로 호출하는 데 권장되는 방법입니다.
제공되는 다양한 라이브러리는 다음과 같습니다.

- [**Kusto.Data(Kusto 클라이언트 라이브러리)** ](./netfx/about-kusto-data.md) - 데이터를 쿼리하고, 메타데이터를 쿼리하고 변경하는 데 사용할 수 있습니다.
- [**Kusto.Ingest(Kusto 수집 라이브러리)** ](netfx/about-kusto-ingest.md) - Kusto.Data를 사용하고, 데이터 수집을 용이하게 하기 위해 확장합니다.


**Kusto 클라이언트 라이브러리**(Kusto.Data)는 Kusto REST API를 기반으로 하여 빌드되고, HTTPS 요청을 대상 Kusto 클러스터에 보냅니다. 

**Kusto 수집 라이브러리**(Kusto.Ingest)는 Kusto.Data를 사용합니다.



위의 모든 라이브러리는 Azure API(예: Azure Storage API, Azure Active Directory API)를 사용합니다.

## <a name="python-libraries"></a>Python 라이브러리

Azure Data Explorer는 호출자에서 데이터 쿼리 및 제어 명령을 보낼 수 있는 Python 클라이언트 라이브러리를 제공합니다.

## <a name="r-library"></a>R 라이브러리

Azure Data Explorer는 호출자에서 데이터 쿼리 및 제어 명령을 보낼 수 있는 R 클라이언트 라이브러리를 제공합니다.



## <a name="using-azure-data-explorer-from-powershell"></a>PowerShell에서 Azure Data Explorer 사용

Azure Data Explorer .NET Framework 라이브러리는 PowerShell 스크립트에서 사용할 수 있습니다.
[PowerShell에서 Azure Data Explorer를 호출](powershell/powershell.md)하면 예제가 제공됩니다.

## <a name="ide-integration"></a>IDE 통합

`monaco-kusto` 패키지는 Microsoft에서 개발한 웹 편집기인 Monaco Editor와의 통합 및 Visual Studio Code의 기초를 지원합니다.
[monaco-kusto](monaco/monaco-kusto.md) 패키지에 대해 자세히 알아보세요.