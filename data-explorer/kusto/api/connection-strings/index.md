---
title: 연결 문자열 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 연결 문자열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 02a459af689cf9f18fe129ee73bff7034a92c80a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342487"
---
# <a name="connection-strings"></a>연결 문자열

연결 문자열은 Kusto 제어 명령, Kusto API 및 때로는 쿼리에서도 널리 사용됩니다.
연결 문자열은 Kusto 외부의 리소스(예: Azure Blob Storage 서비스의 Blob 및 Azure SQL Database 데이터베이스)를 찾고 상호 작용하는 방법을 설명하는 일반적인 수단을 제공합니다.

연결 문자열 형식은 여러 가지입니다.

* [Kusto 연결 문자열](./kusto.md)은 Kusto 서비스 엔드포인트와 통신하는 방법을 설명합니다.
  Kusto 연결 문자열 [ADO.NET 연결 문자열 모델](/dotnet/framework/data/adonet/connection-string-syntax)에 따라 모델링됩니다.
* [Storage 연결 문자열](./storage.md)은 Kusto가 외부 스토리지 서비스(예: Azure Blob Storage 및 Azure Data Lake Storage)를 가리키도록 하는 방법을 설명합니다.
* SQL 연결 문자열 - Kusto [sql_request plugin](../../query/sqlrequestplugin.md)이 Azure DB 서비스에 대한 요청을 실행하고 [SQL 명령으로 내보내는 데 사용됩니다](../../management/data-export/export-data-to-sql.md).  
  이러한 연결 문자열은 [SqlClient 연결 문자열](/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings) 사양을 따릅니다.

> [!NOTE]
> 일부 연결 문자열은 보안 주체를 참조할 수 있습니다. 연결 문자열에서 보안 주체를 지정하는 방법은 [보안 주체 및 ID 공급자](../../management/access-control/principals-and-identity-providers.md)를 참조하세요.