---
title: Azure 데이터 탐색기 Access에 대해 AAD를 사용 하 여 인증 하는 방법-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 azure 데이터 탐색기에서 Azure 데이터 탐색기 액세스에 대해 AAD를 사용 하 여 인증 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: b7e2120f158093e07e096b200b96ac3e265ae2e0
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82861989"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Azure 데이터 탐색기 액세스를 위해 AAD를 사용 하 여 인증 하는 방법

Azure 데이터 탐색기에 액세스 하는 데 권장 되는 방법은 **Azure Active Directory** 서비스 (때로는 **azure AD**또는 단순히 **AAD**)를 인증 하는 것입니다. 이렇게 하면 Azure 데이터 탐색기에서 2 단계 프로세스를 사용 하 여 액세스 하는 보안 주체의 디렉터리 자격 증명을 볼 필요가 없습니다.

1. 첫 번째 단계에서 클라이언트는 AAD 서비스와 통신 하 고, 인증 하 고, 클라이언트에서 액세스 하려는 특정 Azure 데이터 탐색기 끝점에 대해 특별히 발급 된 액세스 토큰을 요청 합니다.
2. 두 번째 단계에서는 클라이언트가 Azure 데이터 탐색기에 대 한 요청을 발급 하 여 Azure 데이터 탐색기에 대 한 id 증명으로 첫 번째 단계에서 획득 한 액세스 토큰을 제공 합니다.

그런 다음 Azure 데이터 탐색기는 AAD에서 액세스 토큰을 발급 한 보안 주체를 대신 하 여 요청을 실행 하 고이 id를 사용 하 여 모든 권한 부여 검사를 수행 합니다.

대부분의 경우에는 Azure 데이터 탐색기 Sdk 중 하나를 사용 하 여 서비스에 프로그래밍 방식으로 액세스 하는 것이 좋습니다 .이는 위의 흐름을 구현 하는 데 많은 부담이 거의 없습니다. 예를 들어 [.NET SDK](../../api/netfx/about-the-sdk.md)를 참조 하세요.
인증 속성은 [Kusto 연결 문자열](../../api/connection-strings/kusto.md)에 의해 설정 됩니다.
가능 하지 않은 경우이 흐름을 직접 구현 하는 방법에 대 한 자세한 내용은을 참조 하세요.

주요 인증 시나리오는 다음과 같습니다.

* **로그인 한 사용자를 인증 하는 클라이언트 응용 프로그램**입니다.
  이 시나리오에서 대화형 (클라이언트) 응용 프로그램은 사용자에 게 자격 증명 (예: 사용자 이름 및 암호)에 대 한 AAD 프롬프트를 트리거합니다.
  [사용자 인증](#user-authentication)을 참조 하세요.

* **"헤드리스" 응용 프로그램**입니다.
  이 시나리오에서 응용 프로그램은 자격 증명을 제공 하는 사용자가 없는 상태로 실행 되며, 대신 응용 프로그램이로 구성 된 일부 자격 증명을 사용 하 여 AAD에 "자체"로 인증 됩니다.
  [응용 프로그램 인증](#application-authentication)을 참조 하세요.

* **인증을 대신**합니다.
  "웹 서비스" 또는 "웹 앱" 시나리오 라고도 하는이 시나리오에서 응용 프로그램은 다른 응용 프로그램에서 AAD 액세스 토큰을 가져온 다음 Azure 데이터 탐색기와 함께 사용할 수 있는 다른 AAD 액세스 토큰으로 변환 합니다.
  즉, 응용 프로그램은 자격 증명과 Azure 데이터 탐색기 서비스를 제공 하는 사용자 또는 응용 프로그램 간의 mediator 역할을 합니다.
  [인증에 대](#on-behalf-of-authentication)한 자세한 내용은을 참조 하세요.

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 AAD 리소스 지정

AAD에서 액세스 토큰을 획득 하는 경우 클라이언트는 토큰을 발급 해야 하는 **aad 리소스** 를 aad에 알려야 합니다. Azure 데이터 탐색기 끝점의 AAD 리소스는 포트 정보와 경로를 제외 하 고 끝점의 URI입니다. 예를 들면 다음과 같습니다.

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>AAD 테 넌 트 ID 지정

AAD는 다중 테 넌 트 서비스 이며, 모든 조직은 AAD에서 **디렉터리** 라는 개체를 만들 수 있습니다. 디렉터리 개체는 사용자 계정, 응용 프로그램 및 그룹과 같은 보안 관련 개체를 포함 합니다. AAD는 종종 디렉터리를 **테 넌 트**로 참조 합니다. AAD 테 넌 트가 GUID (**테 넌 트 ID**)로 식별 됩니다. 대부분의 경우에는 AAD 테 넌 트가 조직의 도메인 이름으로 식별 될 수도 있습니다.

예를 들어 "Contoso" 라는 조직에는 테 넌 트 ID `4da81d62-e0a8-4899-adad-4349ca6bfe24` 및 도메인 이름이 `contoso.com`있을 수 있습니다.

## <a name="specifying-the-aad-authority"></a>AAD 인증 기관 지정

AAD에는 인증을 위한 여러 끝점이 있습니다.

* 인증 되는 보안 주체를 호스트 하는 테 넌 트를 알고 있는 경우 즉, 사용자 또는 응용 프로그램이 있는 AAD 디렉터리를 알고 있는 경우 AAD 끝점은 `https://login.microsoftonline.com/{tenantId}`입니다.
  여기서 `{tenantId}` 는 AAD의 조직의 테 넌 트 ID 또는 해당 도메인 이름 (예: `contoso.com`)입니다.

* 인증 되는 보안 주체를 호스트 하는 테 넌 트를 알 수 없는 경우 "common" 끝점은 `{tenantId}` 위의 값을 값 `common`으로 바꿔 사용할 수 있습니다.

> [!NOTE]
> 인증에 사용 되는 AAD 끝점은 **aad AUTHORITY URL** 또는 단순히 **aad authority**라고도 합니다.

## <a name="aad-token-cache"></a>AAD 토큰 캐시

Azure 데이터 탐색기 SDK를 사용 하는 경우 AAD 토큰은 사용자 단위 토큰 캐시 (로그인 한 사용자만 액세스 하거나 암호를 해독할 수 있는 **%APPDATA%\Kusto\tokenCache.data** 라는 파일)에서 로컬 컴퓨터에 저장 됩니다. 사용자에 게 자격 증명을 묻는 메시지를 표시 하기 전에 캐시에서 토큰을 검사 하 여 사용자가 자격 증명을 입력 해야 하는 횟수를 크게 줄입니다.

> [!NOTE]
> AAD 토큰 캐시를 사용 하면 사용자에 게 Azure 데이터 탐색기에 대 한 액세스를 제공 하는 대화형 프롬프트 수가 줄어들지만 완료는 줄이지 않습니다. 또한 사용자는 자격 증명을 입력 하 라는 메시지가 표시 되 면 미리 예상할 수 없습니다.
> 이는 대화형이 아닌 로그온을 지원 해야 하는 경우 (예: 작업을 예약 하는 경우) 사용자 계정을 사용 하 여 Azure 데이터 탐색기에 액세스 하지 않아야 하는 경우, 로그온 한 사용자에 게 대화형이 아닌 로그온에서 실행 하는 경우 프롬프트에 오류가 발생 하는 경우입니다.


## <a name="user-authentication"></a>사용자 인증

사용자 인증을 사용 하 여 Azure 데이터 탐색기에 액세스 하는 가장 쉬운 방법은 Azure 데이터 탐색기 SDK를 사용 `Federated Authentication` 하 고 azure 데이터 탐색기 연결 문자열의 속성 `true`을로 설정 하는 것입니다. SDK를 처음 사용 하 여 서비스에 요청을 보낼 때 사용자에 게 AAD 자격 증명을 입력 하는 로그인 양식이 표시 되 고 인증에 성공 하면 요청이 전송 됩니다.

Azure 데이터 탐색기 SDK를 사용 하지 않는 응용 프로그램은 AAD 서비스 보안 프로토콜 클라이언트를 구현 하는 대신 AAD 클라이언트 라이브러리 (ADAL)를 계속 사용할 수 있습니다. .NET 응용 프로그램https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet에서 작업을 수행 하는 예제는 []를 참조 하세요.

Azure 데이터 탐색기 액세스에 대 한 사용자를 인증 하려면 먼저 응용 프로그램에 위임 `Access Kusto` 된 권한을 부여 해야 합니다. 자세한 내용은 [Kusto의 AAD 응용 프로그램 프로 비전 가이드](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application) 를 참조 하세요.

다음 간단한 코드 조각은 ADAL을 사용 하 여 Azure 데이터 탐색기에 액세스 하는 AAD 사용자 토큰을 획득 하는 방법을 보여 줍니다 (로그온 UI 시작).

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire user token for the interactive user for Kusto:
AuthenticationResult result = authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", "your client app id",
    new Uri("your client app resource id"), new PlatformParameters(PromptBehavior.Auto)).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="application-authentication"></a>애플리케이션 인증

다음 간단한 코드 조각은 ADAL을 사용 하 여 Azure 데이터 탐색기에 액세스 하는 AAD 응용 프로그램 토큰을 가져오는 방법을 보여 줍니다. 이 흐름에서는 프롬프트가 표시 되지 않으며 aad에 응용 프로그램을 등록 하 고 응용 프로그램 인증을 수행 하는 데 필요한 자격 증명 (예: aad에서 발행 한 앱 키 또는 AAD에 미리 등록 된 X509v2 인증서)을 포함 해야 합니다.

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire application token for Kusto:
ClientCredential applicationCredentials = new ClientCredential("your application client ID", "your application key");
AuthenticationResult result =
        authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", applicationCredentials).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="on-behalf-of-authentication"></a>인증 시

이 시나리오에서 응용 프로그램은 응용 프로그램에서 관리 하는 일부 임의 리소스에 대해 AAD 액세스 토큰을 전송 했으며,이 토큰을 사용 하 여 Azure 데이터 탐색기 리소스에 대 한 새 AAD 액세스 토큰을 획득 하 고,이 토큰을 사용 하 여 응용 프로그램이 원래 AAD 액세스 토큰에 지정 된 보안 주체 대신 Kusto에 액세스할 수 있도록 합니다.

이 흐름을 [OAuth2 토큰 교환 흐름](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)이라고 합니다.
일반적으로 aad를 사용 하는 여러 구성 단계를 수행 해야 하며, 경우에 따라 aad 테 넌 트 구성에 따라 AAD 테 넌 트 관리자의 특별 한 동의가 필요할 수 있습니다.



**1 단계: 응용 프로그램과 Azure 데이터 탐색기 서비스 간의 트러스트 관계 설정**

1. [Azure Portal](https://portal.azure.com/) 를 열고 올바른 테 넌 트에 로그인 했는지 확인 합니다 (포털에 로그인 하는 데 사용 되는 id의 상단/오른쪽 모서리 참조).

2. 리소스 창에서 **Azure Active Directory**을 클릭 한 다음 **앱 등록**합니다.

3. 흐름을 대신해 서를 사용 하는 응용 프로그램을 찾아 엽니다.

4. **API 권한**을 클릭 한 다음 **사용 권한 추가**를 클릭 합니다.

5. **Azure 데이터 탐색기** 라는 응용 프로그램을 검색 하 고 선택 합니다.

6. **User_impersonation/액세스 Kusto를**선택 합니다.

7. **권한 추가**를 클릭 합니다.

**2 단계: 서버 코드에서 토큰 교환 수행**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for for Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**3 단계: Kusto 클라이언트 라이브러리에 토큰을 제공 하 고 쿼리 실행**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}",
    clusterName,
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>웹 클라이언트 (JavaScript) 인증 및 권한 부여



**AAD 응용 프로그램 구성**

> [!NOTE]
> AAD 앱을 설정 하기 위해 수행 해야 하는 표준 [단계](./how-to-provision-aad-app.md) 외에도 aad 응용 프로그램에서 oauth 암시적 흐름을 사용 하도록 설정 해야 합니다. Azure portal의 응용 프로그램 페이지에서 매니페스트를 선택 하 고 oauth2AllowImplicitFlow를 true로 설정 하 여이를 달성할 수 있습니다.

**세부 정보**

클라이언트가 사용자의 브라우저에서 실행 되는 JavaScript 코드 이면 암시적 부여 흐름이 사용 됩니다. 클라이언트 응용 프로그램에 Azure 데이터 탐색기 서비스에 대 한 액세스 권한을 부여 하는 토큰은 리디렉션 URI (URI 조각)의 일부로 성공한 인증 바로 다음에 제공 됩니다. 이 흐름에는 새로 고침 토큰이 제공 되지 않으므로 클라이언트는 장기간 토큰을 캐시 하 고 다시 사용할 수 없습니다.

Native client 흐름과 마찬가지로 두 개의 AAD 응용 프로그램 (서버 및 클라이언트)과 구성 된 관계를 포함 해야 합니다.

AdalJs는 access_token 호출을 수행 하기 전에 id_token을 받아야 합니다.

액세스 토큰은 `AuthenticationContext.login()` 메서드를 호출 하 여 가져오며, access_tokens은를 호출 `Authenticationcontext.acquireToken()`하 여 가져옵니다.

* 올바른 구성을 사용 하 여 AuthenticationContext를 만듭니다.

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* 에 `authContext.login()` 로그인 하지 않은 `acquireToken()` 경우를 시도 하기 전에를 호출 합니다. 로그인 했는지 여부를 확인 하는 좋은 방법은를 호출 `authContext.getCachedUser()` 하 고 반환 `false`되는지 확인 하는 것입니다.
* 페이지가 `authContext.handleWindowCallback()` 로드 될 때마다를 호출 합니다. 이는 AAD에서 리디렉션을 다시 가로채서 조각 URL에서 토큰을 가져와서 캐시 하는 코드 부분입니다.
* 이제 `authContext.acquireToken()` 를 호출 하 여 유효한 ID 토큰이 있는 실제 액세스 토큰을 가져옵니다. AcquireToken에 대 한 첫 번째 매개 변수는 Kusto 서버 AAD 응용 프로그램 리소스 URL이 됩니다.

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* callbackThatUsesTheToken에서 토큰을 Azure 데이터 탐색기 요청에서 전달자 토큰으로 사용할 수 있습니다. 예:

```javascript
var settings = {
    url: "https://" + clusterAndRegionName + ".kusto.windows.net/v1/rest/query",
    type: "POST",
    data: JSON.stringify({
        "db": dbName,
        "csl": query,
        "properties": null
    }),
    contentType: "application/json; charset=utf-8",
    headers: { "Authorization": "Bearer " + token },
    dataType: "json",
    jsonp: false,
    success: function(data, textStatus, jqXHR) {
        if (successCallback !== undefined) {
            successCallback(data.Tables[0]);
        }

    },
    error: function(jqXHR, textStatus, errorThrown) {
        if (failureCallback !==  undefined) {
            failureCallback(textStatus, errorThrown, jqXHR.responseText);
        }

    },
};

$.ajax(settings).then(function(data) {/* do something wil the data */});
```

> 경고-인증할 때 다음과 같은 예외가 발생 하거나 유사한 예외가 발생 합니다.`ReferenceError: AuthenticationContext is not defined`
전역 네임 스페이스에 AuthenticationContext이 없기 때문일 수 있습니다.
불행 하 게 AdalJS는 현재 인증 컨텍스트가 전역 네임 스페이스에 정의 되어 있는 문서화 되지 않은 요구 사항이 있습니다.