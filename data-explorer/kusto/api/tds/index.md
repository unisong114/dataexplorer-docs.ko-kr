---
title: MS-TDS(T-SQL 지원) - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 MS-TDS(T-SQL 지원)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 8aaea26b6c4e8a7f76c4129faeb681791f25ae7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490551"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS(T-SQL 지원)

Kusto는 T-SQL 쿼리 언어의 하위 세트를 사용하여 Microsoft SQL Server 통신 프로토콜(MS-TDS)의 하위 세트를 지원하므로 SQL Server 쿼리 방법을 알고 있는 기존 도구가 Kusto에서 작동할 수 있습니다. 지원되는 클라이언트 중에는 Microsoft Excel, Microsoft Power BI 등이 있습니다.

클라이언트 도구에서 MS-TDS를 통해 Kusto를 쿼리하려면 클라이언트에서 Azure Active Directory 통합 인증을 사용해야 합니다.

Kusto에서 구현한 T-SQL 쿼리 언어에 대한 자세한 내용은 [T-SQL](./t-sql.md)을 참조하세요. 

잘 알려진 일부 클라이언트에서 MS-TDS/T-SQL을 통해 Kusto를 사용하는 방법에 대한 예제는 [MS-TDS 클라이언트 및 Kusto](./clients.md)를 참조하세요.

Kusto 클러스터를 온-프레미스 SQL 서버에 연결된 서버로 구성하는 [SQL 서버에 연결된 서버로서의 Kusto](./linkedserver.md)를 참조하세요.

Kusto에 연결하기 위해 TDS를 통해 AAD를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하는 MS-TDS](./aad.md)를 참조하세요.

TDS 엔드포인트를 통해 네이티브 KQL 쿼리를 실행하는 방법에 대한 자세한 내용은 [TDS를 통한 KQL](./tdskql.md)을 참조하세요. 

마지막으로 SQL Server의 T-SQL 구현과 Kusto 간의 주요 차이점은 [이 항목](./sqlknownissues.md)을 참조하세요.