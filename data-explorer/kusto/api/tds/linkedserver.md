---
title: SQL server에서 연결 된 서버로 kusto as-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 SQL server에서 연결 된 서버로 Kusto를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 103d39f7fdd10f0375e4a530d51cd17f7cdcec51
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799597"
---
# <a name="kusto-as-a-linked-server-from-the-sql-server"></a>SQL server에서 연결 된 서버로 kusto

SQL server 온-프레미스를 사용 하면 연결 된 서버를 연결 하 고 SQL server 및 연결 된 서버에서 데이터를 조인 하는 쿼리를 만들 수 있습니다.

Kusto는 ODBC 연결을 통해 연결 된 서버로 사용할 수 있습니다.
SQL Server 온-프레미스 서비스는 Azure Active Directory (Azure AD)를 사용 하 여 Azure 데이터 탐색기에 연결할 수 있는 active directory 계정 (기본 서비스 계정 아님)을 사용 해야 합니다.

1. SQL Server 2017에 대 한 최신 ODBC 드라이버 (SSMS 18도 제공)를 설치 합니다.https://www.microsoft.com/download/details.aspx?id=56567
1. 특정 Azure 데이터 탐색기 클러스터 및 데이터베이스용으로 ODBC 드라이버에 대 한 DSN 없는 연결 문자열을 준비 `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`합니다. 언어 옵션은 Azure 데이터 탐색기를 조정 하 여 NVARCHAR (4000)로 문자열을 인코딩하는 데 추가 됩니다. 이 해결 방법에 대 한 자세한 내용은 [ODBC](./clients.md#odbc)를 참조 하십시오.
1. 빨간색 화살표가 가리키는 설정을 사용 하 여 연결 된 서버를 만듭니다.

:::image type="content" source="../images/linkedserverconnection.png" alt-text="연결 된 서버 연결":::

1. 빨간색 화살표가 가리키는 설정을 사용 하 여 보안을 정의 합니다. 

:::image type="content" source="../images/linkedserverlogin.png" alt-text="연결 된 서버 로그인":::

Kusto에서 데이터를 쿼리하려면:

```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

> [!NOTE]
> 1. Azure 데이터 탐색기에서 데이터를 추출 하는 데 Kusto [저장 함수](../../query/schema-entities/stored-functions.md) 를 사용 합니다. 저장 된 함수에는 Kusto에서 기본 [KQL](../../query/index.md) 언어로 작성 되 고 지정 된 매개 변수 값으로 제어 되는 효율적인 쿼리를 위해 필요한 모든 논리가 포함 될 수 있습니다. Kusto 저장 함수를 호출 하는 t-sql 구문은 SQL 테이블 형식 함수를 호출 하는 것과 동일 합니다.
> 1. SQL server는 자체 쿼리 내의 연결 된 서버에서 원격 테이블 형식 함수를 호출 하는 것을 지원 하지 않습니다. 이 제한에 대 한 해결 방법은 연결 `OpenQuery` 된 서버에서 원격 쿼리를 실행 하는 데를 사용 하는 것입니다. 이러한 방식으로 테이블 형식 함수는 SQL server 디렉터리에서 호출 되지 않고 연결 된 서버에서 실행 되는 쿼리에서 호출 됩니다. 외부 T-sql 쿼리를 사용 하 여 SQL server의 데이터와 Kusto 저장 함수에서 반환 된 데이터를를 통해 `OpenQuery`조인할 수 있습니다.
