---
title: AZURE 활성 디렉터리를 갖춘 MS-TDS - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Azure Active Directory를 사용하여 MS-TDS에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/02/2019
ms.openlocfilehash: e70f4e9fa4d831d3a1c2eeb60f07a959a65e478e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523514"
---
# <a name="ms-tds-with-azure-active-directory"></a>Azure 활성 디렉토리를 사용 하 고 MS-TDS

## <a name="aad-user-authentication"></a>AAD 사용자 인증

AAD 사용자 인증을 지원하는 SQL 클라이언트는 Kusto와 함께 사용할 수 있습니다.

### <a name="net-sql-client-user"></a>.NET SQL 클라이언트(사용자)

예를 들어 통합 AAD의 경우:
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        Authentication = SqlAuthenticationMethod.ActiveDirectoryIntegrated,
        DataSource = "mykusto.kusto.windows.net"
    };
```

Kusto는 이미 얻은 액세스 토큰으로 인증을 지원합니다.
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

## <a name="aad-application-authentication"></a>AAD 응용 프로그램 인증

Kusto용으로 프로비전된 AAD 응용 프로그램은 Kusto에 연결하는 데 AAD를 지원하는 SQL 클라이언트 라이브러리를 사용할 수 있습니다. AAD 응용 프로그램에 대한 자세한 내용은 [AAD 응용 프로그램 만들기를](../../management/access-control/how-to-provision-aad-app.md) 참조하십시오.

### <a name="net-sql-client-application"></a>.NET SQL 클라이언트(응용 프로그램)

*ApplicationClientId* 및 *ApplicationKey를* 사용하여 AAD 응용 프로그램을 프로비전하고 클러스터 *ClusterDnsName에서*데이터베이스 *데이터베이스에* 액세스할 수 있는 권한을 부여했다고 가정하면 다음 샘플에서는 이 AAD 응용 프로그램의 쿼리에 .NET SQL Client를 사용하는 방법을 보여 줍니다.

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