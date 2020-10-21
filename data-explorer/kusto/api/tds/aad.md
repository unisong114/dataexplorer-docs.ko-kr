---
title: MS-TDS Azure Active Directory-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 Azure Active Directory에 대 한 MS TDS를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 01/02/2019
ms.openlocfilehash: 262842c7117dd5d7a9737f1038197ae93321985f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343320"
---
# <a name="ms-tds-with-azure-active-directory"></a>Azure Active Directory 포함 된 TDS

## <a name="azure-ad-user-authentication"></a>Azure AD 사용자 인증

Azure AD 사용자 인증을 지 원하는 SQL 클라이언트는 Azure 데이터 탐색기와 함께 사용할 수 있습니다.

### <a name="net-sql-client-user"></a>.NET SQL 클라이언트 (사용자)

예를 들어 통합 Azure AD의 경우:
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        Authentication = SqlAuthenticationMethod.ActiveDirectoryIntegrated,
        DataSource = "mykusto.kusto.windows.net"
    };
```

Kusto는 이미 가져온 액세스 토큰으로 인증을 지원 합니다.
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        DataSource = "mykusto.kusto.windows.net"
    };
    using (var connection = new SqlConnection(csb.ToString()))
    {
        connection.AccessToken = accessToken;
        await connection.OpenAsync();
    }
```

### <a name="jdbc-user"></a>JDBC (사용자)

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.Statement;
import com.microsoft.sqlserver.jdbc.SQLServerDataSource;
import com.microsoft.aad.adal4j.*;

public class Sample {
  public static void main(String[] args) throws Exception {
    AuthenticationResult authenticationResult = futureAuthenticationResult.get();
    SQLServerDataSource ds = new SQLServerDataSource();
    ds.setServerName("<your cluster DNS name>");
    ds.setDatabaseName("<your database name>");
    ds.setHostNameInCertificate("*.kusto.windows.net"); // Or appropriate regional domain.
    ds.setAuthentication("ActiveDirectoryIntegrated");
    try (Connection connection = ds.getConnection();
         Statement stmt = connection.createStatement();) {
      ResultSet rs = stmt.executeQuery("<your T-SQL query>");
      /*
      Read query result.
      */
    } catch (Exception e) {
      System.out.println();
      e.printStackTrace();
    }
  }
}
```

## <a name="azure-ad-application-authentication"></a>Azure AD 응용 프로그램 인증

Kusto에 대해 프로 비전 된 azure AD 응용 프로그램은 Kusto에 연결 하기 위해 Azure AD를 지 원하는 SQL 클라이언트 라이브러리를 사용할 수 있습니다. Azure AD 응용 프로그램에 대 한 자세한 내용은 [AZURE Ad 응용 프로그램 만들기](../../../provision-azure-ad-app.md)를 참조 하세요.

### <a name="net-sql-client-application"></a>.NET SQL 클라이언트 (응용 프로그램)

*Applicationclientid* 및 *applicationclientid* 를 사용 하 여 azure ad 응용 프로그램을 프로 비전 하 고 Cluster *ClusterDnsName*의 데이터베이스 *DatabaseName* 에 액세스할 수 있는 권한을 부여 하는 경우 다음 샘플에서는이 Azure AD 응용 프로그램의 쿼리에 .net SQL 클라이언트를 사용 하는 방법을 보여 줍니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;
using System.Data;
using System.Data.SqlClient;

namespace Sample
{
  class Program
  {
    private static async Task<string> ObtainToken()
    {
      var authContext = new AuthenticationContext(
        // Can also use tenant ID.
        "https://login.microsoftonline.com/<your AAD tenant name>");
      var applicationCredentials = new ClientCredential(
        "<your application client ID>",
        "<your application key>");
      var result = await authContext.AcquireTokenAsync(
        "https://<your cluster DNS name>",
        applicationCredentials);
      return result.AccessToken;
    }

    private static async Task QuerySample()
    {
      var csb = new SqlConnectionStringBuilder()
      {
        InitialCatalog = "<your database name>",
        DataSource = "<your cluster DNS name>"
      };
      using (var connection = new SqlConnection(csb.ToString()))
      {
        connection.AccessToken = await ObtainToken();
        await connection.OpenAsync();
        using (var command = new SqlCommand(
          "<your T-SQL query>",
          connection))
        {
          var reader = await command.ExecuteReaderAsync();
          /*
          Read query result.
          */
        }
      }
    }
  }
}
```

### <a name="jdbc-application"></a>JDBC (응용 프로그램)

```java
import java.sql.*;
import com.microsoft.sqlserver.jdbc.*;
import com.microsoft.aad.adal4j.*;

public class Sample {
  public static void main(String[] args) throws Throwable {
    ExecutorService service = Executors.newFixedThreadPool(1);
    // Can also use tenant name.
    String url = "https://login.microsoftonline.com/<your AAD tenant ID>";
    AuthenticationContext authenticationContext =
      new AuthenticationContext(url, false, service);
    ClientCredential  clientCredential = new ClientCredential(
      "<your application client ID>",
      "<your application key>");
    Future<AuthenticationResult> futureAuthenticationResult =
      authenticationContext.acquireToken(
        "https://<your cluster DNS name>",
        clientCredential,
        null);
    AuthenticationResult authenticationResult = futureAuthenticationResult.get();
    SQLServerDataSource ds = new SQLServerDataSource();
    ds.setServerName("<your cluster DNS name>");
    ds.setDatabaseName("<your database name>");
    ds.setAccessToken(authenticationResult.getAccessToken());
    connection = ds.getConnection();
    statement = connection.createStatement();
    ResultSet rs = statement.executeQuery("<your T-SQL query>");
    /*
    Read query result.
    */
  }
}
```