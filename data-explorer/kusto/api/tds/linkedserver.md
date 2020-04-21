---
title: KUsto SQL 서버에서 연결된 서버로 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Kusto를 Azure 데이터 탐색기의 SQL 서버에서 연결된 서버로 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 77db975f2bd7c5c1d747902248070664cd020e39
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523463"
---
# <a name="kusto-as-linked-server-from-sql-server"></a>SQL 서버에서 연결된 서버로 쿠스토

SQL 서버 온-프레미스는 연결된 서버를 연결할 수 있습니다. 이 기능을 사용하면 SQL 서버와 연결된 서버에서 데이터를 조인하는 쿼리를 만들 수 있습니다.

ODBC 연결을 통해 Kusto를 연결된 서버로 사용할 수 있습니다.

1. SQL Server(온-프레미스) 서비스는 AAD를 사용하여 Kusto에 연결할 수 있는 활성 디렉터리 계정(기본 서비스 계정이 아님)을 사용해야 합니다.
2. SQL Server 2017용 최신 ODBC 드라이버를 설치합니다(SSMS 18도 함께 제공됨).https://www.microsoft.com/download/details.aspx?id=56567
3. 특정 Kusto 클러스터 및 데이터베이스에 대해 ODBC 드라이버에 대한 DSN 이하 연결 문자열을 `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`준비합니다. 언어 옵션은 Kusto를 튜닝하여 문자열을 NVARCHAR(4000)로 인코딩하는 데 추가됩니다. 이 해결 에 대한 자세한 내용은 [ODBC에서](./clients.md#odbc)확인하십시오.
4. 다음 3가지 설정으로 연결된 서버 만들기: ![대체 텍스트](../images/linkedserverconnection.png "연결된 서버 연결")
5. 보안 탭은 이 설정으로 정의되어야 ![합니다.](../images/linkedserverlogin.png "연결된 서버 로그인")

이제 다음과 같이 Kusto에서 데이터를 쿼리할 수 있습니다.
```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

참고:
1. Kusto에서 데이터를 추출하기 위해 Kusto [에 저장된 함수를](../../query/schema-entities/stored-functions.md) 사용하는 것이 좋습니다. 저장된 함수에는 Kusto의 효율적인 쿼리에 필요한 모든 논리가 포함될 수 있으며, 기본 [KQL](../../query/index.md) 언어로 작성되고 지정된 매개 변수 값으로 제어됩니다. Kusto 저장 함수를 호출하는 T-SQL 구문은 SQL 테이블 형식 함수를 호출하는 것과 동일합니다.
2. SQL Server는 자체 쿼리 내에서 연결된 서버에서 원격 테이블 형식 함수 호출을 지원하지 않습니다. 이 제한에 대 한 `OpenQuery` 해결 방법은 링크 된 서버에서 원격 쿼리를 실행 하는 데 사용 하는 것입니다. 이렇게 하면 테이블 형식 함수가 SQL Server directy가 아니라 연결된 서버에서 실행되는 쿼리에서 호출됩니다. 외부 T-SQL 쿼리는 SQL 서버의 데이터와 Kusto 저장 함수에서 반환된 `OpenQuery`데이터 간에 조인하는 데 사용할 수 있습니다.