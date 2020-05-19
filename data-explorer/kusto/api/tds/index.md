---
title: MS-TDS T-SQL 지원 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 MS-TDS T-SQL 지원을 소개합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: a128db995c78c0583bc7c7712c06292a2f6598d1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550540"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS T-SQL 지원

Azure Data Explorer(Kusto)는 T-SQL 쿼리 언어의 하위 세트를 사용하여 Microsoft SQL Server 통신 프로토콜(MS-TDS)의 하위 세트를 지원합니다. Microsoft Excel 및 Microsoft Power BI는 Azure Data Explorer(Kusto)와 함께 사용할 수 있는 많은 도구 중 일부입니다. 이러한 Microsoft 애플리케이션은 SQL Server를 쿼리하는 방법도 알고 있습니다.

> [!NOTE]
> MS-TDS를 통해 Kusto를 쿼리하려면 Azure Active Directory(Azure AD) 통합 인증을 클라이언트 도구로 사용합니다.

## <a name="next-steps"></a>다음 단계

* [T-SQL](./t-sql.md) - Kusto에서 구현한 T-SQL 쿼리 언어에 대해 자세히 알아봅니다. 

* [TDS를 통한 KQL](./tdskql.md) - TDS 엔드포인트를 통해 네이티브 KQL 쿼리를 실행하는 방법에 대해 자세히 알아봅니다.

* [MS-TDS 클라이언트 및 Kusto](./clients.md) - MS-TDS/T-SQL을 사용하는 잘 알려진 클라이언트에서 Azure Data Explorer를 사용합니다.

* [SQL 서버에 연결된 서버로서의 Azure Data Explorer(Kusto)](./linkedserver.md) - 클러스터를 온-프레미스 SQL 서버에 연결된 서버로 구성합니다. 

* [Azure Active Directory를 포함하는 MS-TDS](./aad.md) - Azure Data Explorer에 연결하기 위해 TDS를 통해 Azure AD를 사용합니다.

* [SQL 알려진 문제](./sqlknownissues.md) - SQL Server의 T-SQL 구현과 Azure Data Explorer의 주요 차이점에 대해 자세히 알아봅니다.
