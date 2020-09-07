---
title: Azure Data Explorer API 개요 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 API에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2020
ms.openlocfilehash: b9fd03bfd08a31d872ca3c0ef48bd96514e9eb18
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428399"
---
# <a name="azure-data-explorer-api-overview"></a>Azure Data Explorer API 개요

Azure Data Explorer 서비스에서 지원하는 엔드포인트는 다음과 같습니다.

1. Azure Data Explorer에서 데이터를 쿼리하고 관리할 수 있는 [REST API](#rest-api) 엔드포인트입니다.
   이 엔드포인트는 쿼리 및 [제어 명령](../management/index.md)에 대한 [Kusto 쿼리 언어](../query/index.md)를 지원합니다.
1. Microsoft SQL Server 제품에서 사용하는 Microsoft TDS(Tabular Data Stream) 프로토콜의 하위 집합을 구현하는 [MS-TDS](#ms-tds) 엔드포인트입니다.
   이 엔드포인트는 쿼리를 위해 SQL Server 엔드포인트와 통신하는 방법을 알고 있는 도구에 유용합니다.
1. Azure 서비스에 대한 표준 수단인 [ARM(Azure Resource Manager)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) 엔드포인트입니다. 이 엔드포인트는 Azure Data Explorer 클러스터와 같은 리소스를 관리하는 데 사용됩니다.

## <a name="rest-api"></a>REST API

Azure Data Explorer 서비스와 통신하는 기본적인 방법은 서비스의 REST API를 사용하는 것입니다. 완전히 문서화된 이 엔드포인트를 통해 호출자는 다음을 수행할 수 있습니다.

* 쿼리 데이터
* 메타데이터 쿼리 및 수정
* 데이터 수집
* 서비스 상태 쿼리
* 리소스 관리

서로 다른 Azure Data Explorer 서비스는 공개적으로 사용할 수 있는 동일한 REST API를 통해 서로 통신합니다.

또한 REST API 프로토콜을 처리하지 않고 서비스를 사용할 수 있는 [클라이언트 라이브러리](client-libraries.md)가 많이 있습니다.

## <a name="ms-tds"></a>MS-TDS

또한 Azure Data Explorer는 Microsoft SQL Server 통신 프로토콜(MS-TDS)을 지원하며 T-SQL 쿼리 실행에 대한 제한된 지원을 포함합니다. 이 프로토콜을 통해 사용자는 잘 알려진 쿼리 구문(T-SQL)과 데이터베이스 클라이언트 도구(예: LINQPad, sqlcmd, Tableau, Excel 및 Power BI)를 사용하여 Azure Data Explorer에서 쿼리를 실행할 수 있습니다.

자세한 내용은 [MS-TDS](tds/index.md)를 참조하세요.

## <a name="client-libraries"></a>클라이언트 라이브러리 

Azure Data Explorer는 프로그래밍 방식으로 쉽게 액세스할 수 있도록 위의 엔드포인트를 사용하는 여러 클라이언트 라이브러리를 제공합니다.

* .NET SDK
* Python SDK
* R
* Java SDK
* Node SDK
* Go SDK
* PowerShell

### <a name="net-framework-libraries"></a>.NET Framework 라이브러리

.NET Framework 라이브러리는 Azure Data Explorer 기능을 프로그래밍 방식으로 호출하는 데 권장되는 방법입니다.
제공되는 다양한 라이브러리는 다음과 같습니다.

* [Kusto.Data(Kusto 클라이언트 라이브러리)](./netfx/about-kusto-data.md): 데이터를 쿼리하고, 메타데이터를 쿼리하고, 변경할 수 있습니다. 
   Kusto REST API를 기반으로 빌드되고, HTTPS 요청을 대상 Kusto 클러스터에 보냅니다.
* [Kusto.Ingest(Kusto 수집 라이브러리)](netfx/about-kusto-ingest.md): `Kusto.Data`를 사용하고 확장하여 데이터 수집을 용이하게 합니다.

위의 라이브러리는 Azure API(예: Azure Storage API, Azure Active Directory API)를 사용합니다.

### <a name="python-libraries"></a>Python 라이브러리

Azure Data Explorer는 호출자가 데이터 쿼리 및 제어 명령을 보내도록 허용하는 Python 클라이언트 라이브러리를 제공합니다.
자세한 내용은 [Azure Data Explorer Python SDK](python/kusto-python-client-library.md)를 참조하세요.

### <a name="r-library"></a>R 라이브러리

Azure Data Explorer는 호출자가 데이터 쿼리 및 제어 명령을 보내도록 허용하는 R 클라이언트 라이브러리를 제공합니다.
자세한 내용은 [Azure Data Explorer R SDK](r/kusto-r-client-library.md)를 참조하세요.

### <a name="java-sdk"></a>Java SDK

Java 클라이언트 라이브러리는 Java를 사용하여 Azure Data Explorer 클러스터를 쿼리하는 기능을 제공합니다. 자세한 내용은 [Azure Data Explorer Java SDK](java/kusto-java-client-library.md)를 참조하세요.

### <a name="node-sdk"></a>Node SDK

Azure Data Explorer Node SDK는 Node LTS(현재 v6.14)와 호환되며 ES6를 사용하여 빌드됩니다.
자세한 내용은 [Azure Data Explorer Node SDK](node/kusto-node-client-library.md)를 참조하세요.

### <a name="go-sdk"></a>Go SDK

Azure Data Explorer Go 클라이언트 라이브러리는 Go를 사용하는 Azure Data Explorer 클러스터에 쿼리, 제어, 수집 기능을 제공합니다. 자세한 내용은 [Azure Data Explorer Golang SDK](golang/kusto-golang-client-library.md)를 참조하세요.

### <a name="powershell"></a>PowerShell

Azure Data Explorer .NET Framework 라이브러리는 PowerShell 스크립트에서 사용할 수 있습니다. 자세한 내용은 [PowerShell에서 Azure Data Explorer 호출](powershell/powershell.md)을 참조하세요.

## <a name="monaco-ide-integration"></a>모나코 IDE 통합

`monaco-kusto` 패키지는 Monaco 웹 편집기와의 통합을 지원합니다.
Microsoft에서 개발한 Monaco Editor는 Visual Studio Code의 기반입니다.
자세한 내용은 [monaco-kusto 패키지](monaco/monaco-kusto.md)를 참조하세요.
