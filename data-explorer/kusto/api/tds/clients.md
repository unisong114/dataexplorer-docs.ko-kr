---
title: MS-TDS 클라이언트 및 쿠스토 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 AZURE 데이터 탐색기에서 MS-TDS 클라이언트 및 Kusto에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 233eec65c14d76b25b76cc85c7ddd190e5171dfe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523497"
---
# <a name="ms-tds-clients-and-kusto"></a>MS-TDS 클라이언트 와 쿠스토

Kusto는 MS-SQL 클라이언트에 대한 TDS 컴플렌드 엔드포인트를 구현합니다. 호환성이 프로토콜 수준에 있기 때문에 Azure Active Directory 인증을 사용하여 SQL Azure 데이터베이스에 연결할 수 있는 모든 라이브러리 또는 응용 프로그램은 Kusto 서버와 직교적으로 운영 체제 또는 런타임에 사용됩니다. SQL Azure 서버처럼 Kusto 서버 도메인 이름을 사용하십시오.

SQL 언어 수준에서 Kusto는 T-SQL의 하위 집합과 SQL 서버 에뮬레이션의 하위 집합을 구현합니다. SQL Server의 T-SQL 구현과 Kusto 간의 주요 차이점중 일부에 대해 [알아봅슬합니다.](./sqlknownissues.md)

## <a name="net-sql-client"></a>.NET SQL 클라이언트

Kusto는 SQL 클라이언트에 대한 Azure Active Directory 인증을 지원합니다.

자세한 내용은 [.NET SQL 클라이언트(사용자 인증)](./aad.md#net-sql-client-user) 및 [.NET SQL 클라이언트(응용 프로그램 인증)를](./aad.md#net-sql-client-application) 참조하십시오.



## <a name="jdbc"></a>JDBC

마이크로 소프트 JDBC 드라이버는 AAD 인증쿠스토에 연결하는 데 사용할 수 있습니다.

*mssql-jdbc* JAR 및 *adal4j* JAR 및 모든 종속성 버전 중 하나를 사용하여 응용 프로그램을 만듭니다. 다음은 그 예입니다.

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

JDBC 드라이버 클래스를 사용하도록 어플리케이션 만들기`com.microsoft.sqlserver.jdbc.SQLServerDriver`

다음과 같은 연결 문자열을 사용합니다.

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

AAD 통합 인증 모드를 사용하려는 경우 *ActiveDirectory통합으로 ActiveDirectoryPassword를* 대체합니다. *ActiveDirectoryIntegrated*

자세한 내용은 [JDBC(사용자 인증)](./aad.md#jdbc-user) 및 [JDBC(응용 프로그램 인증)를](./aad.md#jdbc-application) 참조하십시오.

## <a name="odbc"></a>ODBC

ODBC를 지원하는 응용 프로그램은 Kusto에 연결할 수 있습니다.

ODBC 데이터 원본 만들기:
1. ODBC 데이터 원본 관리자를 시작합니다.
2. 새 데이터 원본(단추)을 `Add`만듭니다.
3. `ODBC Driver 17 for SQL Server`를 선택합니다.
3. 데이터 원본에 이름을 지정하고 `Server` 필드에 Kusto 클러스터 이름을 지정합니다(예:`mykusto.kusto.windows.net`
4. 인증 `Active Directory Integrated` 옵션을 선택합니다.
5. 다음 탭에서 데이터베이스를 선택할 수 있습니다.
7. 다음 탭에서 다른 모든 설정에 대한 기본값을 남길 수 있습니다.
8. `Finish`단추를 누르면 연결을 테스트할 수 있는 데이터 원본 요약 대화 상자가 열립니다.
9. ODBC 소스는 이제 응용 프로그램과 함께 사용할 수 있습니다.

ODBC 응용 프로그램이 DSN 대신 또는 DSN 이외에 연결 문자열을 허용할 수 있는 경우 다음 연결 문자열을 사용합니다.
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

일부 ODBC 응용 프로그램은 NVARCHAR(MAX) 유형에서 https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver 잘 작동하지 않습니다(자세한 내용은 참조). 이러한 응용 프로그램에 사용되는 일반적인 해결 방법은 NVARCHAR(n)에 반환된 데이터를 캐스팅하는 것입니다. Kusto는 문자열 유형이 하나뿐이고 SQL 클라이언트의 경우 NVARCHAR(MAX)로 인코딩되므로 이러한 해결 방법은 Kusto에서 작동하지 않습니다. Kusto는 이러한 응용 프로그램에 대해 다른 해결 방법을 제공합니다. 연결 문자열을 통해 모든 문자열을 NVARCHAR(n)로 인코딩하도록 Kusto를 구성할 수 있습니다. 연결 문자열의 언어 필드를 사용하여 튜닝 옵션을 형식으로 지정할 수 있습니다. `language@OptionName1:OptionValue1,OptionName2:OptionValue2` 

예를 들어 다음 연결 문자열은 Kusto에게 문자열을 NVARCHAR(8000)로 인코딩하도록 지시합니다.
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

ODBC 드라이버를 사용하는 PowerShell 스크립트의 예:

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  

```



## <a name="linqpad"></a>LINQPad

그것은 SQL 서버처럼 Kusto에 연결하여 Kusto와 Linq 응용 프로그램을 사용할 수 있습니다.
LINQPad는 Linq 호환성을 탐색하는 데 사용할 수 있습니다. 또한 Kusto를 찾아보고 SQL 쿼리를 실행하는 데 사용할 수 있습니다.
LINQPad는 KUSto TDS(SQL) 끝점을 탐색하는 데 권장되는 도구입니다.

Microsoft SQL 서버에 연결하는 것처럼 연결합니다. LINQPad는 Active Directory 인증을 지원합니다.

1. `Add connection`을 클릭합니다.
2. `Build data context automatically`을 선택합니다.
3. LINQPad 드라이버를 `Default (LINQ to SQL)`선택합니다.
4. 공급자는 을 `SQL Azure`선택합니다.
5. 서버의 경우 Kusto 클러스터의 이름을 지정합니다(예:`mykusto.kusto.windows.net`
6. 로그인을 선택할 `Windows Authentication (Active Directory)`수 있습니다.
7. 버튼을 `Test` 클릭하여 연결을 확인합니다.
8. 버튼을 `OK` 클릭합니다. 브라우저 창에는 데이터베이스가 있는 트리 보기가 표시됩니다.
9. 데이터베이스, 테이블 및 열을 탐색할 수 있습니다.
10. 쿼리 창에서 SQL 쿼리를 실행할 수 있습니다. SQL 언어를 지정하고 데이터베이스에 대한 연결을 선택합니다.
11. 쿼리 창에서 LINQ 쿼리를 실행할 수도 있습니다. 예를 들어 브라우저 창에서 테이블을 마우스 오른쪽 단추로 클릭합니다. 옵션을 `Count` 선택합니다. 그것은 실행하자.

## <a name="azure-data-studio-134-and-above"></a>Azure 데이터 스튜디오(1.3.4 이상)

1. 새 연결.
2. 연결 유형 `Microsoft SQL Server`선택: .
3. Kusto 클러스터의 이름을 서버 이름으로 지정합니다(예:`mykusto.kusto.windows.net`
4. 인증 유형 `Azure Active Directory - Universal with MFA support`선택: .
5. AAD에 프로비전된 계정(예: `myname@contoso.com` 처음 계정 추가)을 지정합니다.
6. 데이터베이스 선택기를 사용하여 데이터베이스를 선택할 수 있습니다.
7. `Connect`버튼을 누르면 데이터베이스 대시보드로 이동하게 됩니다.
8. 연결을 마우스 오른쪽 `New Query` 단추로 클릭하고 쿼리 `New Query` 탭을 열거나 대시보드에서 작업을 클릭합니다.

## <a name="power-bi-desktop"></a>Power BI Desktop

SQL Azure 데이터베이스에 연결하는 것처럼 연결합니다.

1. 에서 `Get Data` `More`선택 `Azure` 한 다음`Azure SQL Database`
2. Kusto 서버 이름(예: 지정)`mykusto.kusto.windows.net`
3. "직접 쿼리" 옵션을 사용합니다.
4. 인증을 `Microsoft account` 선택하지 `Windows`않음을 `sign in`선택하고 을 클릭합니다.
5. 선택기에는 사용 가능한 데이터베이스가 표시됩니다. 실제 SQL 서버와 마찬가지로 계속합니다.

## <a name="excel"></a>Excel

SQL Azure 데이터베이스에 연결하는 것처럼 연결합니다.

1. 탭에서 `Data` `Get Data`, `From Azure``From Azure SQL Database`
2. Kusto 서버 이름(예: 지정)`mykusto.kusto.windows.net`
3. 인증을 `Microsoft account` 선택하지 `Windows`않음을 `sign in`선택하고 을 클릭합니다.
4. 로그인한 후 `Connect`을 클릭합니다.
5. 선택기에는 사용 가능한 데이터베이스가 표시됩니다. 실제 SQL 서버와 마찬가지로 계속합니다.

## <a name="tableau"></a>Tableau

1. ODBC 데이터 원본을 생성하고 [ODBC](./clients.md#odbc) 섹션을 참조하십시오.
2. 를 `Other Databases (ODBC)`통해 연결합니다.
3. 에서 ODBC 데이터 `DSN`원본을 선택합니다.
4. 단추를 사용하여 `Connect` 연결을 설정합니다.
5. 버튼을 `Sign In` 사용할 수 있으면 Kusto에 로그인합니다.

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 이상)

Kusto와 호환되는 방식으로 결과 집합을 처리하도록 DBeaver를 구성합니다.

1. 메뉴에서 `Window` `Preferences`선택합니다.
2. `Editors` 섹션에서 을 `Data Editor`선택합니다.
3. 옵션이 `Refresh data on next page reading` 선택되어 있는지 확인합니다.

Kusto 데이터베이스에 대한 연결 만들기:

1. 새 데이터베이스 연결(메뉴`Database` `New Connection` 및 옵션)을 만듭니다.
2. 을 `Azure` 찾아서 `Azure SQL Database`선택합니다. `Next`를 누릅니다.
3. 호스트(예: `mykusto.kusto.windows.net` 및 데이터베이스)를 지정합니다. `mydatabase` 마스터를 데이터베이스 이름으로 두지 마십시오. Kusto는 특정 데이터베이스에 연결해야 합니다.
4. 인증 옵션의 `Active Directory - Password`경우 를 선택합니다.
5. 활성 디렉터리 사용자의 자격 증명(예: `myname@contoso.com` 이 사용자의 해당 암호)을 지정합니다.
6. 를 `Test Connection …` 눌러 연결 세부 정보가 올바른지 확인합니다.

## <a name="microsoft-sql-server-management-studio-v18x"></a>마이크로소프트 SQL 서버 관리 스튜디오 (v18.x)

1. 에서 `Object Explorer` `Connect`. `Database Engine`
2. Kusto 클러스터의 이름을 서버 이름으로 지정합니다(예:`mykusto.kusto.windows.net`
3. 인증에 옵션을 사용합니다. `Active Directory - Integrated`
4. `Options`을 클릭합니다.
5. 콤보에서 `Connect to database` 옵션을 통해 `Browse Server` 사용 가능한 데이터베이스를 탐색할 수 있습니다.
6. 브라우징을 진행하려면 클릭합니다. `Yes`
7. 대화 상자에는 사용 가능한 모든 데이터베이스가 있는 트리 보기가 표시됩니다. 하나를 클릭하여 데이터베이스에 대한 연결을 진행할 수 있습니다.
8. 또는 콤보에서 `Connect to database` 을 선택할 `default`수 있습니다. `Connect`을 클릭합니다. 개체 탐색기는 모든 데이터베이스를 표시합니다.
9. SSMS는 상호 연관된 하위 쿼리를 사용하여 데이터베이스 스키마를 찾아보기 때문에 SSMS를 통해 데이터베이스 개체를 검색하는 것은 아직 지원되지 않습니다. 상관 하위 쿼리는 Kusto에서 지원되지 않으며 [상관 하위 쿼리를](./sqlknownissues.md#correlated-sub-queries)참조하십시오.
10. 데이터베이스를 클릭합니다. 옵션을 `New Query` 클릭하여 쿼리 창을 엽니다.
11. 쿼리 창에서 사용자 지정 SQL 쿼리를 실행할 수 있습니다.

## <a name="matlab-via-jdbc"></a>매트랩 (JDBC 를 통해)

MATLAB의 정적 클래스 경로 앞에 필요한 JAR 파일을 추가합니다. 이렇게 하려면 기본 설정 디렉토리에 *"javaclasspath.txt"* 파일을 만듭니다. Matlab의 명령 창에서 다음 명령을 실행합니다. 

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

필요한 JAR 파일에 전체 경로를 추가합니다. 

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> 이 파일을 클래스 경로 의 맨 앞에 추가하려면 맨 위에> *전에* <필요합니다. 또한 "c:\full\path\to"를 이러한 파일에 대한 실제 전체 경로로 바꿉습니다. 

MATLAB을 다시 시작하여 이러한 클래스가 실제로 로드되었는지 확인합니다.

연결을 시도하기 전에 다음 명령 (MATLAB 명령 창)을 실행합니다.

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> 이렇게 하면 *변압기가* 기본값으로 재설정됩니다(MATLAB은 일반적으로 색슨과 함께 오버로드하지만 ADAL4J와 호환되지 않습니다).

Kusto TDS 끝점에 연결하려면 다음 명령(MATLAB 명령 창)을 제출합니다.

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> 이것은 단순히 *"database="로* 끝나고 이름이 없는 "데이터베이스" 함수가 이 문자열에 첫 번째 입력(데이터베이스 이름)을 자동으로 더하는 것이 맞습니다.
> AAD 통합 인증 모드를 사용하려는 경우 *ActiveDirectory통합으로 ActiveDirectoryPassword를* 대체합니다. *ActiveDirectoryIntegrated*

연결을 테스트하고 샘플 쿼리를 실행합니다. 명령에 따라 다이 제출 (MATLAB 명령 창):

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> *KUSTO_TABLE* 쿠스토의 기존 테이블로 바꾸기



## <a name="sending-t-sql-queries-over-the-rest-api"></a>나머지 API를 통해 T-SQL 쿼리 보내기

[Kusto REST API는](../rest/index.md) T-SQL 쿼리를 수락하고 실행할 수 있습니다.
이렇게 하려면 T-SQL 쿼리 자체의 텍스트로 `csl` 설정된 속성과 요청 [속성이](../netfx/request-properties.md) `OptionQueryLanguage` 값으로 `sql`설정된 속성을 사용하여 쿼리 끝점에 요청을 보냅니다.