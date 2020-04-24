---
title: Kusto 연결 문자열-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 연결 문자열에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: bf31d8573266de1217ce93944a357c716d3ba508
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108170"
---
# <a name="kusto-connection-strings"></a>Kusto 연결 문자열

Kusto 연결 문자열은 kusto 클라이언트 응용 프로그램이 Kusto 서비스 끝점에 대 한 연결을 설정 하는 데 필요한 정보를 제공할 수 있습니다. Kusto 연결 문자열은 ADO.NET 연결 문자열 다음에 모델링 됩니다. 즉, 연결 문자열은 세미콜론으로 구분 된 이름/값 매개 변수 쌍의 목록이 며, 선택적으로 접두사가 붙은 단일 URI입니다.

**예제:**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

URI는와 통신할 서비스 끝점을 제공 합니다.

* (`https://help.kusto.windows.net`)- `Data Source` 속성의 값입니다.
* `Samples`(기본 데이터베이스)-`Initial Catalog` 속성의 값입니다.

이름/값 구문을 사용 하 여 두 가지 추가 속성이 제공 됩니다. 

* `Fed`속성 (라고도 함 `AAD Federated Security`)이로 `true`설정 됩니다.
* `Accept`속성이로 `true`설정 됩니다.

> [!NOTE]
>
> * 속성 이름은 대/소문자를 구분 하지 않으며 이름/값 쌍 간의 공백은 무시 됩니다.
> * 속성 값 **은** 대/소문자를 구분 합니다. 세미콜론 (`;`), 작은따옴표 (`'`) 또는 큰따옴표 (`"`)를 포함 하는 속성 값은 큰따옴표로 묶어야 합니다.

몇 가지 kusto 클라이언트 도구는 간단한 형식 `@` _ClusterName_ `/` _InitialCatalog_ 을 사용할 수 있도록 연결 문자열의 URI 접두사에 대 한 확장을 지원 합니다.
예 `@help/Samples` 를 들어 연결 문자열은 세 가지 속성 ( `https://help.kusto.windows.net/Samples; Fed=true``Data Source`, `Initial Catalog`및 `AAD Federated Security`)을 나타내는로 변환 됩니다.

프로그래밍 방식으로 c # `Kusto.Data.KustoConnectionStringBuilder` 클래스에서 Kusto 연결 문자열을 구문 분석 하 고 조작할 수 있습니다. 이 클래스는 모든 연결 문자열의 유효성을 검사 하 고 유효성 검사가 실패 하는 경우 런타임 예외를 생성 합니다.
이 기능은 Kusto SDK의 모든 버전에서 제공 됩니다.

## <a name="connection-string-properties"></a>연결 문자열 속성

다음 표에서는 Kusto 연결 문자열에서 지정할 수 있는 모든 속성을 보여 줍니다.
별칭 인 추가 속성 이름 뿐만 아니라 프로그래밍 이름 ( `Kusto.Data.KustoConnectionStringBuilder` 개체의 속성 이름)을 나열 합니다.

### <a name="general-properties"></a>일반 속성

| 속성 이름              | 대체 이름                      | 프로그래밍 이름  | 설명                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| 추적을 위한 클라이언트 버전 |                                        | TraceClientVersion | 클라이언트 버전을 추적 하는 경우이 값을 사용 합니다.   |
| 데이터 원본                | 주소, 주소, 네트워크 주소, 서버 | DataSource         | Kusto 서비스 끝점을 지정 하는 URI입니다. 예를 들어 `https://mycluster.kusto.windows.net` 또는 `net.tcp://localhost`와 같습니다.               |
| 초기 카탈로그            | 데이터베이스                               | InitialCatalog     | 기본적으로 사용할 데이터베이스의 이름입니다. 예: MyDatabase|
| 쿼리 일관성          | QueryConsistency                       | QueryConsistency   | 를 `strongconsistency` 또는 `weakconsistency` 로 설정 하 여 쿼리를 실행 하기 전에 메타 데이터와 동기화 해야 하는지 확인 합니다. |

### <a name="user-authentication-properties"></a>사용자 인증 속성

| 속성 이름          | 대체 이름                          | 프로그래밍 이름 | 설명                       |
|------------------------|--------------------------------------------|-------------------|-----------------------------------|
| AAD 페더레이션된 보안 | 페더레이션된 보안, 페더레이션된, 급지됨, AADFed | FederatedSecurity | Azure Active를 수행 하도록 클라이언트에 지시 하는 부울 값입니다.  |
| MFA 적용            | MFA, EnforceMFA                             | EnforceMfa        | 다단계 인증 토큰을 획득 하도록 클라이언트에 지시 하는 부울 값입니다.       |
| 사용자 ID                | UID, 사용자                                  | UserID            | 지정 된 사용자 이름으로 사용자 인증을 수행 하도록 클라이언트에 지시 하는 문자열 값입니다.           |
| 추적할 사용자 이름  |                                            | TraceUserName     | 요청을 내부적으로 추적할 때 사용할 사용자 이름을 서비스에 보고 하는 문자열 값입니다.         |
| 사용자 토큰             | UsrToken, UserToken                        | UserToken         | 지정 된 전달자 토큰을 사용 하 여 클라이언트에서 사용자 인증을 수행 하도록 지시 하는 문자열 값입니다.<br/>ApplicationClientId, Applicationclientid 및 Applicationclientid을 재정의 합니다. (지정 된 경우)는 제공 된 토큰을 선호 하는 실제 클라이언트 인증 흐름을 건너뜁니다.       |
| 네임스페이스              | NS                                         | 네임스페이스         | (나중에 사용 하기 위해)  |



### <a name="application-authentication-properties"></a>응용 프로그램 인증 속성

|속성 이름                                     |대체 이름                         |프로그래밍 이름                             |설명      |
|--------------------------------------------------|------------------------------------------|----------------------------------------------|-----------------|
|AAD 페더레이션된 보안                            |페더레이션된 보안, 페더레이션된, 급지됨, AADFed|FederatedSecurity                             |AAD (Azure Active Directory) 페더레이션 인증을 수행 하도록 클라이언트에 지시 하는 부울 값입니다.|
|응용 프로그램 인증서 지문                |AppCert                                   |ApplicationCertificateThumbprint              |응용 프로그램 클라이언트 인증서 인증 흐름을 사용할 때 사용할 클라이언트 인증서의 지문을 제공 하는 문자열 값입니다.|
|응용 프로그램 클라이언트 Id                             |AppClientId                               |ApplicationClientId                           |인증할 때 사용할 응용 프로그램 클라이언트 ID를 제공 하는 문자열 값입니다.|
|애플리케이션 키                                   |AppKey                                    |ApplicationKey                                |응용 프로그램 비밀 흐름을 사용 하 여 인증할 때 사용할 응용 프로그램 키를 제공 하는 문자열 값입니다.|
|추적할 응용 프로그램 이름                      |TraceAppName                              |ApplicationNameForTracing                     |요청을 내부적으로 추적할 때 사용할 응용 프로그램 이름을 서비스에 보고 하는 문자열 값입니다.|
|응용 프로그램 토큰                                 |AppToken                                  |ApplicationToken                              |지정 된 전달자 토큰을 사용 하 여 응용 프로그램 인증을 수행 하도록 클라이언트에 지시 하는 문자열 값입니다.|
|권한 Id                                      |TenantId                                  |Authority                                     |응용 프로그램이 등록 된 테 넌 트의 이름 또는 ID를 제공 하는 문자열 값입니다.|
|                                                  |                                          |EmbeddedManagedIdentity                       |관리 되는 id 인증에 사용할 응용 프로그램 id를 클라이언트에 게 지시 하는 문자열 값입니다. 시스템 `system` 할당 id를 나타내는 데 사용 합니다. 이 속성은 연결 문자열을 사용 하 여 프로그래밍 방식 으로만 설정할 수 없습니다.|ManagedServiceIdentity                        |TODO|
|응용 프로그램 인증서 주체 고유 이름|응용 프로그램 인증서 주체           |ApplicationCertificateSubjectDistinguishedName||
|응용 프로그램 인증서 발급자 고유 이름 |응용 프로그램 인증서 발급자            |ApplicationCertificateIssuerDistinguishedName ||
|응용 프로그램 인증서 공개 인증서 보내기   |응용 프로그램 인증서 SendX5c, SendX5c  |ApplicationCertificateSendPublicCertificate   ||



### <a name="client-communication-properties"></a>클라이언트 통신 속성

|속성 이름                      |대체 이름|프로그래밍 이름  |설명                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|수락      ||수락      |실패 시 반환 될 자세한 오류 개체를 요청 하는 부울 값입니다.|
|스트리밍   ||스트리밍   |호출자에 게 제공 하기 전에 클라이언트에서 데이터를 누적 하지 않도록 요청 하는 부울 값입니다.|
|미압축||미압축|클라이언트에서 전송 수준 압축을 요청 하지 않도록 요청 하는 부울 값입니다.|

## <a name="authentication-properties-details"></a>인증 속성 (세부 정보)

연결 문자열의 중요 한 작업 중 하나는 클라이언트에 게 서비스에 대 한 인증 방법을 알려 주는 것입니다.
일반적으로 클라이언트는 HTTP/HTTPS 끝점에 대 한 인증에 사용 되는 알고리즘은 다음과 같습니다.

1. AadFederatedSecurity가 true 인 경우:
    1. UserToken가 지정 된 경우 지정 된 토큰을 사용 하 여 AAD 페더레이션된 인증을 사용 합니다.
    1. 그렇지 않고 ApplicationToken이 지정 된 경우 지정 된 토큰을 사용 하 여 페더레이션 인증을 수행 합니다.
    1. 그렇지 않고 ApplicationClientId 및 Applicationclientid가 지정 된 경우 지정 된 응용 프로그램 클라이언트 ID 및 키를 사용 하 여 페더레이션 인증을 수행 합니다.
    1. 그렇지 않고 ApplicationClientId 및 Applicationclientid가 지정 된 경우 지정 된 응용 프로그램 클라이언트 ID 및 인증서를 사용 하 여 페더레이션 인증을 수행 합니다.
    1. 그렇지 않으면 현재 로그온 한 사용자의 id를 사용 하 여 페더레이션된 인증을 수행 합니다 (사용자가 세션의 첫 번째 인증 인지 묻는 메시지가 표시 됨).

1. 그렇지 않으면 인증 하지 않습니다.





### <a name="aad-federated-application-authentication-with-application-certificate"></a>응용 프로그램 인증서를 사용 하 여 AAD 페더레이션된 응용 프로그램 인증

1. 응용 프로그램의 인증서를 기반으로 하는 인증은 웹 응용 프로그램에 대해서만 지원 되며 native client 응용 프로그램에 대해서는 지원 되지 않습니다.
1. 지정 된 인증서를 수락 하도록 웹 응용 프로그램을 구성 해야 합니다. [기반 AAD 응용 프로그램의 인증서를 인증 하는 방법](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
1. 웹 응용 프로그램은 관련 Kusto 클러스터에서 인증 된 주체로 구성 되어야 합니다.
1. 지정 된 지문이 있는 인증서를 로컬 컴퓨터 저장소 또는 현재 사용자 저장소에 설치 해야 합니다.
1. 인증서의 공개 키는 2048 비트 이상을 포함 해야 합니다.

## <a name="aad-based-authentication-examples"></a>AAD 기반 인증 예

**현재 로그온 한 사용자의 id를 기반으로 하는 AAD 페더레이션된 인증 (필요한 경우 사용자에 게 표시 됨)**

```csharp
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
  .WithAadUserPromptAuthentication(authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;authority={authority}"
```

**지정 된 ApplicationClientId 및 Applicationclientid에 기반 하 여 AAD 페더레이션된 응용 프로그램 인증**

```csharp
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = APP_GUID;
var applicationKey = secret;
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};authority={authority}"
```

**지정 된 사용자/응용 프로그램의 토큰을 기반으로 하는 AAD 페더레이션된 인증**

```csharp
var serviceNameAndRegion = "help";
var databaseName = "NetDefaultDB";
var clusterAndDatabase = string.Format(
    "https://{0}.kusto.windows.net/{1}",
    serviceNameAndRegion, databaseName);

// AAD User - Option 1
var userToken = "<UserToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
    .WithAadUserTokenAuthentication(userToken);

// AAD User - Option 2
var userToken = "<UserToken>";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
{
    FederatedSecurity = true,
    UserToken = userToken,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;UserToken={user_token};authority={authority}"

// AAD Application - Option 1
var applicationToken = "<ApplicationToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
    .WithAadApplicationTokenAuthentication();

// AAD Application - Option 2
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationToken = "<UserToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
{
    FederatedSecurity = true,
    ApplicationToken = applicationToken,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppToken={applicationToken};authority={authority}"
```

**인증서 지문 사용 (클라이언트는 로컬 저장소에서 인증서를 로드 하려고 시도)**

```csharp
var serviceNameAndRegion = "help";
var databaseName = "Samples";
var clusterAndDatabase = string.Format(
    "https://{0}.kusto.windows.net/{1}",
    serviceNameAndRegion, databaseName);

string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Option 2
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};authority={authority}"
```

