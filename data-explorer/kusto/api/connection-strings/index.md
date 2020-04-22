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
ms.openlocfilehash: 53c3caecc373a646162016fc1717ce1b0b0b85d1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490075"
---
# <a name="connection-strings"></a>연결 문자열

연결 문자열은 Kusto 제어 명령, Kusto API 및 때로는 쿼리에서도 널리 사용됩니다.
연결 문자열은 Kusto 외부의 리소스(예: Azure Blob Storage 서비스의 Blob 및 Azure SQL Database 데이터베이스)를 찾고 상호 작용하는 방법을 설명하는 일반적인 수단을 제공합니다.

연결 문자열 형식은 여러 가지입니다.

* [Kusto 연결 문자열](./kusto.md)은 Kusto 서비스 엔드포인트와 통신하는 방법을 설명합니다.
  Kusto 연결 문자열 [ADO.NET 연결 문자열 모델](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax)에 따라 모델링됩니다.
* [Storage 연결 문자열](./storage.md)은 Kusto가 외부 스토리지 서비스(예: Azure Blob Storage 및 Azure Data Lake Storage)를 가리키도록 하는 방법을 설명합니다.
* SQL 연결 문자열 - Kusto [sql_request plugin](../../query/sqlrequestplugin.md)이 Azure DB 서비스에 대한 요청을 실행하고 [SQL 명령으로 내보내는 데 사용됩니다](../../management/data-export/export-data-to-sql.md).  
  이러한 연결 문자열은 [SqlClient 연결 문자열](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings) 사양을 따릅니다.