---
title: MS-TDS 클라이언트 및 Kusto-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 MS TDS 클라이언트 및 Kusto에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/30/2019
ms.openlocfilehash: b41f77fe97ce6adeeade63c00824818f4a3af721
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862040"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>MS-TDS 클라이언트 및 Azure 데이터 탐색기

Azure 데이터 탐색기는 MS SQL 클라이언트에 대 한 TDS 규격 끝점을 구현 합니다. 호환성은 프로토콜 수준에 있습니다. Azure Active Directory (Azure AD) 인증을 사용 하 여 SQL Azure 데이터베이스에 연결할 수 있는 모든 라이브러리나 응용 프로그램은 Azure 데이터 탐색기 서버와 함께 작동 합니다. 따라서 SQL Azure 서버와 같이 서버 도메인 이름을 사용할 수 있습니다.

Azure 데이터 탐색기는 T-sql 및 SQL server 에뮬레이션의 하위 집합을 구현 합니다. 자세한 내용은 SQL Server에서 T-sql 및 Azure 데이터 탐색기의 구현 간의 차이점에 대 한 [알려진 문제](./sqlknownissues.md) 를 참조 하세요.

## <a name="net-sql-client"></a>.NET SQL 클라이언트

Azure 데이터 탐색기는 SQL 클라이언트에 대 한 Azure AD 인증을 지원 합니다. 자세한 내용은 [.NET Sql 클라이언트 (사용자 인증)](./aad.md#net-sql-client-user) 및 [.net sql 클라이언트 (응용 프로그램 인증)](./aad.md#net-sql-client-application) 를 참조 하세요.

## <a name="jdbc"></a>JDBC

Microsoft JDBC driver는 Azure AD 인증을 사용 하 여 Azure 데이터 탐색기에 연결 하는 데 사용할 수 있습니다.

*Mssql-jdbc* jar 및 *adal4j* jar 및 모든 해당 종속성의 버전 중 하나를 사용 하는 응용 프로그램을 만듭니다.
예를 들면 다음과 같습니다.

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

JDBC 드라이버 클래스 *SQLServerDriver*를 사용 하는 응용 프로그램을 만듭니다.

다음과 같은 연결 문자열을 사용 합니다.

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> Azure Active Directory 통합 인증 모드를 사용 하려면 *ActiveDirectoryPassword* 를 *ActiveDirectoryIntegrated*로 바꿉니다. 자세한 내용은 [jdbc (사용자 인증)](./aad.md#jdbc-user) 및 [jdbc (응용 프로그램 인증)](./aad.md#jdbc-application)를 참조 하세요.

## <a name="odbc"></a>ODBC

ODBC를 지 원하는 응용 프로그램은 Azure 데이터 탐색기에 연결할 수 있습니다.

ODBC 데이터 원본 만들기:

1. ODBC 데이터 원본 관리자를 시작합니다.
2. **추가** 를 선택 하 여 새 데이터 원본을 만들고 *SQL Server ODBC Driver 17*을 설정 합니다.
3. 데이터 원본에 이름을 지정 하 고 **서버** 필드에 Azure 데이터 탐색기 클러스터 이름을 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
4. 인증 옵션에 대 한 **Active Directory 통합**을 설정 합니다.
5. **다음** 을 선택 하 여 데이터베이스를 설정 합니다.
7. 다음 탭에서 다른 모든 설정에 대 한 기본값을 그대로 둘 수 있습니다.
8. **마침** 을 선택 하 여 연결을 테스트할 수 있는 데이터 원본 요약 창을 엽니다.

이제 응용 프로그램과 함께 ODBC 데이터 원본을 사용할 수 있습니다.

ODBC 응용 프로그램에서 DSN 외에 또는 대신 연결 문자열을 허용할 수 있는 경우 다음을 사용 합니다.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

일부 ODBC 응용 프로그램은 형식에서 `NVARCHAR(MAX)` 제대로 작동 하지 않습니다. 자세한 내용은 https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver를 참조하세요.

일반적인 해결 방법은 반환 된 데이터를 *NVARCHAR (n)* 로 캐스팅 하는 것입니다. 여기에는 n의 값이 있습니다. 예를 들어 *NVARCHAR (4000)* 입니다. 그러나 Azure 데이터 탐색기에는 문자열 유형이 하나 뿐이 고 SQL 클라이언트는 *NVARCHAR (MAX)* 로 인코딩되어 있으므로 azure 데이터 탐색기에 대 한 해결 방법은 작동 하지 않습니다.

Azure 데이터 탐색기는 다른 해결 방법을 제공 합니다. 연결 문자열을 통해 모든 문자열을 *NVARCHAR (n)* 로 인코딩하려면 Azure 데이터 탐색기를 구성할 수 있습니다. 연결 문자열의 언어 필드를 사용 하 여 * language@OptionName1:OptionValue1OptionName2: OptionValue2*형식으로 튜닝 옵션을 지정할 수 있습니다.

예를 들어 다음 연결 문자열은 문자열을 *NVARCHAR (8000)* 로 인코딩하려면 Azure 데이터 탐색기에 지시 합니다.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

ODBC 드라이버를 사용 하는 PowerShell 스크립트의 예는 다음과 같습니다.

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()
```

## <a name="linqpad"></a>LINQPad

Linq 응용 프로그램은 SQL server와 같이 연결 하 여 Azure 데이터 탐색기와 함께 사용할 수 있습니다.
LINQPad를 사용 하 여 Linq 호환성을 탐색 하 고 Azure 데이터 탐색기을 찾아봅니다. 또한 SQL 쿼리를 실행할 수 있으며 Azure 데이터 탐색기 TDS (SQL) 끝점을 탐색 하는 데 권장 되는 도구입니다.

Microsoft SQL Server와 마찬가지로 연결 합니다. LINQPad은 Active Directory 인증을 지원 합니다.

1. **연결 추가**를 선택합니다.
2. **빌드 데이터 컨텍스트를 자동으로**설정 합니다.
3. LINQPad 드라이버의 **기본값 (LINQ to SQL)** 을 설정 합니다.
4. **SQL Azure**설정 합니다.
5. 서버에 대해 Azure 데이터 탐색기 클러스터의 이름을 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
6. 로그인에 대 한 **Windows 인증 (Active Directory)** 을 설정 합니다.
7. **테스트** 를 선택 하 여 연결을 확인 합니다.
8. **확인**을 선택합니다. 브라우저 창에 데이터베이스와 함께 트리 뷰가 표시 됩니다.
9. 데이터베이스, 테이블 및 열을 탐색할 수 있습니다.
10. 쿼리 창에서 SQL 쿼리를 실행할 수 있습니다. SQL 언어를 지정 하 고 데이터베이스에 대 한 연결을 선택 합니다.
11. 쿼리 창에서 LINQ 쿼리를 실행할 수도 있습니다. 예를 들어 브라우저 창에서 테이블을 선택 합니다. **Count**를 선택 하 고 실행 하도록 합니다.

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 이상)

새 연결을 설정 합니다.

1. 연결 형식을 **Microsoft SQL Server**로 설정 합니다.
2. 서버 이름으로 Azure 데이터 탐색기 클러스터의 이름을 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
3. **MFA 지원으로**인증 유형 Azure Active Directory을 설정 합니다.
4. Azure AD에서 프로 비전 되는 계정을 지정 합니다. 예를 들어 *myname@contoso.com* 입니다. 처음으로 계정을 추가 합니다.
5. 데이터베이스 선택기를 사용 하 여 데이터베이스를 선택 합니다.
6. **연결** 을 선택 하 여 데이터베이스 대시보드로 이동 하 고 연결을 설정 합니다.
7. **새 쿼리** 를 선택 하 여 쿼리 창을 열거나 대시보드에서 **새 쿼리** 태스크를 선택 합니다.

## <a name="power-bi-desktop"></a>Power BI Desktop

SQL Azure 데이터베이스에 연결 하는 것과 같습니다.

1. **데이터 가져오기**에서 **자세히**를 선택 합니다.
2. **Azure**를 설정 하 고 **Azure SQL Database**합니다.
3. Azure 데이터 탐색기 서버 이름을 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
4. **DirectQuery**를 선택 합니다.
5. **Microsoft 계정** 인증 ( **Windows**아님)을 설정 하 고 **로그인**을 선택 합니다.
6. 데이터베이스 선택은 사용 가능한 데이터베이스를 표시 합니다. 실제 SQL server를 사용 하 여 수행 하는 것 처럼 계속 합니다.

## <a name="excel"></a>Excel

SQL Azure 데이터베이스에 연결 하는 것과 같습니다.

1. **데이터** 탭에서 **데이터 가져오기** 를 선택한 다음 **Azure에서** 설정 하 고 **Azure SQL Database**합니다.
2. Azure 데이터 탐색기 서버 이름을 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
3. **Microsoft 계정** 인증 ( **Windows**아님)을 설정 하 고 **로그인**을 선택 합니다.
5. 데이터베이스 선택은 사용 가능한 데이터베이스를 표시 합니다. 실제 SQL server를 사용 하 여 수행 하는 것 처럼 계속 합니다.
4. 로그인 한 후 **연결**을 선택 합니다.
5. 데이터베이스 선택은 사용 가능한 데이터베이스를 표시 합니다. 실제 SQL server를 사용 하 여 수행 하는 것 처럼 계속 합니다.

## <a name="tableau"></a>Tableau

ODBC 데이터 원본을 만듭니다. 자세한 내용은 [ODBC](./clients.md#odbc) 섹션을 참조 하십시오.

1. **다른 데이터베이스를 통해 연결 (ODBC)**
2. **DSN**에서 ODBC 데이터 원본을 설정 합니다.
3. 연결 **을 선택 하 여 연결을 설정** 합니다.
4. 단추를 사용할 수 있게 되 면 **로그인**을 선택 하 고 Azure 데이터 탐색기에 로그인 합니다.

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 이상)

Azure 데이터 탐색기와 호환 되는 방식으로 결과 집합을 처리 하는 DBeaver를 구성 합니다.

1. **창** 메뉴에서 **기본 설정** 을 선택 합니다.
2. **편집기** 섹션에서 **데이터 편집기** 를 선택 합니다.
3. **다음 페이지를 읽을 때 데이터 새로 고침** 이 표시 되어 있는지 확인 합니다.

Azure 데이터 탐색기 데이터베이스에 대 한 연결을 만듭니다.

1. **데이터베이스** 메뉴에서 **새 연결** 을 선택 합니다.
2. **Azure** 를 찾고 **Azure SQL Database**를 설정 합니다. **다음**을 선택합니다.
3. 호스트를 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
4. 데이터베이스를 지정 합니다. 예: *mydatabase*.

> [!WARNING]
> *Master* 를 데이터베이스 이름으로 사용 하지 마세요. Azure 데이터 탐색기에는 특정 데이터베이스에 대 한 연결이 필요 합니다.

5. *인증*을 위해 **Active Directory 암호** 를 설정 합니다.
6. Active directory 사용자의 자격 증명을 지정 합니다. *myname@contoso.com*예를 들어, 및이 사용자에 해당 하는 암호를 설정 합니다.
7. **연결 테스트** ...를 선택 합니다. 연결 정보가 올바른지 확인 합니다.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio (v18)

1. **연결**을 선택한 다음 **개체 탐색기**에서 **데이터베이스 엔진** 합니다.
2. 서버 이름으로 Azure 데이터 탐색기 클러스터의 이름을 지정 합니다. 예를 들면 *mykusto.kusto.windows.net*입니다.
3. 인증을 위해 **Active Directory 통합** 을 설정 합니다.
4. **옵션**을 선택 합니다.
5. **데이터베이스에 연결** 아래에서 **서버 찾아보기** 를 선택 하 여 사용 가능한 데이터베이스를 검색 합니다.
6. 찾아보기를 계속 하려면 **예** 를 선택 합니다.
7. 창에 사용 가능한 모든 데이터베이스가 포함 된 트리 뷰가 표시 됩니다. 하나를 선택 하 여 데이터베이스에 연결 합니다.
8. 또 다른 이유는 **데이터베이스에 연결**에서 **기본값** 을 선택 하 고 **연결**을 선택 하는 것입니다. 개체 탐색기에 모든 데이터베이스가 표시 됩니다.

> [!NOTE]
> SSMS는 상관 관계 하위 쿼리를 사용 하 여 데이터베이스 스키마를 검색 하므로 SSMS를 통한 데이터베이스 개체 검색은 아직 지원 되지 않습니다.
> Azure 데이터 탐색기는 상호 관련 된 하위 쿼리를 지원 하지 않습니다. 자세한 내용은 [상관 하위 쿼리](./sqlknownissues.md#correlated-sub-queries)를 참조 하세요.

10. **새 쿼리** 를 선택 하 여 쿼리 창을 열고 데이터베이스를 설정 합니다.

쿼리 창에서 사용자 지정 SQL 쿼리를 실행할 수 있습니다.

## <a name="matlab-via-jdbc"></a>MATLAB (JDBC를 통해)

기본 설정 디렉터리에서 *"javaclasspath"* 파일을 만들어 MATLAB의 정적 클래스 경로 앞에 필요한 JAR 파일을 추가 합니다.

1. Matlab의 명령 창에서 다음 명령을 실행 합니다.

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. 필요한 JAR 파일에 전체 경로를 추가 합니다.

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

> [!NOTE]
> 이러한 파일이 클래스 경로 앞에 추가 되도록 맨 위에> **하기 전에** <필요 합니다. 또한 *c:\full\path\to* 를 이러한 파일의 실제 전체 경로로 바꿉니다.

3. MATLAB를 다시 시작 하 여 이러한 클래스가 로드 되는지 확인 합니다.

4. 연결을 시도 하기 전에 다음 명령을 실행 합니다 (MATLAB 명령 창).

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> 이렇게 하면 **TransformerFactory** 이 기본값으로 다시 설정 됩니다. 일반적으로 MATLAB는 **Saxon**를 사용 하 여 오버 로드 하지만 **ADAL4J**와는 호환 되지 않습니다.

5. 다음 명령을 사용 하 여 Azure 데이터 탐색기 TDS 끝점에 연결 합니다 (MATLAB 명령 창).

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> **"Database ="** 로 끝난 후에는 값을 사용 하지 않는 것이 좋습니다. *데이터베이스* 함수는 첫 번째 입력 인 데이터베이스 이름을이 문자열에 자동으로 추가 합니다.
> Azure Active Directory 통합 인증 모드를 사용 하려면 *ActiveDirectoryPassword* 를 *ActiveDirectoryIntegrated*로 바꿉니다.

6. 연결을 테스트 하 고 샘플 쿼리를 실행 합니다. 다음 명령을 제출 합니다 (MATLAB 명령 창).

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> *KUSTO_TABLE* 를 Azure 데이터 탐색기의 기존 테이블로 바꿉니다.

## <a name="sending-t-sql-queries-over-the-rest-api"></a>REST API에서 T-sql 쿼리 보내기

[Azure 데이터 탐색기 REST API](../rest/index.md) 는 t-sql 쿼리를 수락 하 고 실행할 수 있습니다.

1. **Csl** 속성이 t-sql 쿼리 텍스트로 설정 된 쿼리 끝점에 요청을 보냅니다.
2. **[요청 속성](../netfx/request-properties.md)** **OptionQueryLanguage** 을 **sql**로 설정 합니다.
