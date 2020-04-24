---
title: Azure 데이터 탐색기 액세스에 대한 AAD로 인증하는 방법 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Azure 데이터 탐색기 액세스에 대한 AAD로 인증하는 방법에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: dc3a87a290ac535ac475af5017170a0478cd9b54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522919"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Azure 데이터 탐색기 액세스에 대한 AAD로 인증하는 방법

Azure 데이터 탐색기에서 액세스하는 권장 방법은 **Azure Active Directory** 서비스(Azure **AD**또는 단순히 **AAD라고도**함)를 인증하는 것입니다. 이렇게 하면 Azure Data Explorer에서 2단계 프로세스를 사용하여 액세스 주체의 디렉터리 자격 증명을 볼 수 없습니다.

1. 첫 번째 단계에서 클라이언트는 AAD 서비스와 통신하고 인증하며 클라이언트가 액세스하려는 특정 Azure Data Explorer 끝점에 대해 특별히 발급된 액세스 토큰을 요청합니다.
2. 두 번째 단계에서 클라이언트는 Azure Data Explorer에 요청을 발행하여 첫 번째 단계에서 획득한 액세스 토큰을 Azure Data Explorer에 ID 증명으로 제공합니다.

그런 다음 Azure Data Explorer는 AAD가 액세스 토큰을 발급한 보안 주체를 대신하여 요청을 실행하고 이 ID를 사용하여 모든 권한 부여 검사가 수행됩니다.

대부분의 경우 Azure Data Explorer SDK 중 하나를 사용하여 프로그래밍 방식으로 서비스에 액세스하는 것이 좋습니다. 예를 들어 [.NET SDK](../../api/netfx/about-the-sdk.md)를 참조하십시오.
인증 속성은 [Kusto 연결 문자열에](../../api/connection-strings/kusto.md)의해 설정됩니다.
가능하지 않은 경우 이 흐름을 직접 구현하는 방법에 대한 자세한 정보를 읽어보십시오.

주요 인증 시나리오는 다음과 같습니다.

* **로그인한 사용자를 인증하는 클라이언트 응용 프로그램입니다.**
  이 시나리오에서 대화형(클라이언트) 응용 프로그램은 자격 증명(예: 사용자 이름 및 암호)에 대한 AAD 프롬프트를 사용자에게 트리거합니다.
  [사용자 인증](#user-authentication)을 참조하십시오.

* **"헤드리스" 응용 프로그램.**
  이 시나리오에서는 자격 증명을 제공하기 위해 사용자가 없는 응용 프로그램이 실행되고 대신 응용 프로그램이 구성된 일부 자격 증명을 사용하여 AAD에 "자체"로 인증합니다.
  [응용 프로그램 인증을](#application-authentication)참조하십시오.

* **인증을 대신합니다.**
  "웹 서비스" 또는 "웹 앱" 시나리오라고도 하는 이 시나리오에서 응용 프로그램은 다른 응용 프로그램에서 AAD 액세스 토큰을 받은 다음 Azure Data Explorer에서 사용할 수 있는 다른 AAD 액세스 토큰으로 "변환"합니다.
  즉, 응용 프로그램은 자격 증명을 제공한 사용자 또는 응용 프로그램과 Azure Data Explorer 서비스 간의 중재자 역할을 합니다.
  [인증을 대신](#on-behalf-of-authentication)참조합니다.

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Azure 데이터 탐색기의 AAD 리소스 지정

AAD에서 액세스 토큰을 획득할 때 클라이언트는 AAD에 토큰을 발행해야 하는 **AAD 리소스를** 알려야 합니다. Azure 데이터 탐색기 끝점의 AAD 리소스는 포트 정보와 경로를 금지하는 끝점의 URI입니다. 다음은 그 예입니다.

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>AAD 테넌트 ID 지정

AAD는 다중 테넌트 서비스이며 모든 조직은 AAD에서 **디렉터리라는** 개체를 만들 수 있습니다. 디렉터리 개체에는 사용자 계정, 응용 프로그램 및 그룹과 같은 보안 관련 개체가 포함됩니다. AAD는 종종 디렉터리를 **테넌트라고**합니다. AAD 테넌트는**GUID(테넌트 ID)로**식별됩니다. 대부분의 경우 AAD 테넌은 조직의 도메인 이름으로 식별할 수도 있습니다.

예를 들어 "Contoso"라는 조직에테넌트 `4da81d62-e0a8-4899-adad-4349ca6bfe24` ID와 도메인 `contoso.com`이름이 있을 수 있습니다.

## <a name="specifying-the-aad-authority"></a>AAD 권한 지정

AAD에는 인증을 위한 여러 끝점이 있습니다.

* 인증되는 보안 주체를 호스팅하는 테넌트가 알려진 경우(즉, 사용자 또는 응용 프로그램이 있는 AAD 디렉터리를 `https://login.microsoftonline.com/{tenantId}`알고 있는 경우) AAD 끝점은 .
  `{tenantId}` 다음은 AAD에 있는 조직의 테넌트 ID 또는 도메인 이름(예: )입니다. `contoso.com`

* 인증되는 보안 주체를 호스팅하는 테넌트를 알 수 없는 경우 `{tenantId}` 위의 값을 값으로 `common`대체하여 "공통" 끝점을 사용할 수 있습니다.

> [!NOTE]
> 인증에 사용되는 AAD 끝점을 **AAD 기관 URL** 또는 단순히 **AAD 기관이라고도 합니다.**

## <a name="aad-token-cache"></a>AAD 토큰 캐시

Azure 데이터 탐색기 SDK를 사용하는 경우 AAD 토큰은 사용자별 토큰 캐시(로그인한 사용자만 액세스하거나 해독할 수 있는 **%APPDATA%\Kusto\tokenCache.data라는** 파일)의 로컬 컴퓨터에 저장됩니다. 캐시는 사용자에게 자격 증명을 입력하기 전에 토큰을 검사하므로 사용자가 자격 증명을 입력해야 하는 횟수가 크게 줄어듭니다.

> [!NOTE]
> AAD 토큰 캐시는 사용자에게 Azure Data Explorer에 액세스하여 표시될 대화형 프롬프트의 수를 줄이지만 완료를 줄이지는 않습니다. 또한 사용자는 자격 증명에 대한 메시지가 표시될 때 미리 예측할 수 없습니다.
> 즉, 비대화형 로그온(예: 작업을 예약하는 경우)을 지원해야 하는 경우 사용자 계정을 사용하여 Azure Data Explorer에 액세스하려고 시도해서는 안 됩니다.


## <a name="user-authentication"></a>사용자 인증

사용자 인증을 사용하여 Azure Data 탐색기 탐색기에 액세스하는 가장 쉬운 `Federated Authentication` 방법은 Azure Data Explorer SDK를 사용하고 Azure Data 탐색기 연결 문자열의 속성을 로 설정하는 것입니다. `true` SDK가 서비스에 요청을 보내는 데 처음 사용될 때 사용자에게 AAD 자격 증명을 입력하기 위한 로그인 양식이 표시되고 성공적인 인증시 요청이 전송됩니다.

Azure 데이터 탐색기 SDK를 사용하지 않는 응용 프로그램은 AAD 서비스 보안 프로토콜 클라이언트를 구현하는 대신 AAD 클라이언트 라이브러리(ADAL)를 계속 사용할 수 있습니다. .NEThttps://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet응용 프로그램에서 이 작업을 수행하는 예제는 [] 참조하십시오.

Azure Data Explorer 액세스에 대한 사용자를 인증하려면 먼저 `Access Kusto` 응용 프로그램에 위임된 권한이 부여되어야 합니다. 자세한 내용은 [AAD 응용 프로그램 프로비저닝에 대한 Kusto 가이드를](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application) 참조하십시오.

다음 간단한 코드 조각은 ADAL을 사용하여 AAD 사용자 토큰을 획득하여 Azure Data Explorer(로그온 UI 시작)에 액세스하는 방법을 보여 줍니다.

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

다음 간단한 코드 조각은 ADAL을 사용하여 AAD 응용 프로그램 토큰을 획득하여 Azure 데이터 탐색기액세스에 액세스하는 방법을 보여 줍니다. 이 흐름에서는 프롬프트가 표시되지 않으며 응용 프로그램에 AAD에 등록하고 응용 프로그램 인증을 수행하는 데 필요한 자격 증명(예: AAD에서 발급한 앱 키 또는 AAD에 사전 등록된 X509v2 인증서)이 있어야 합니다.

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

## <a name="on-behalf-of-authentication"></a>인증을 대신합니다.

이 시나리오에서 응용 프로그램은 응용 프로그램에서 관리하는 일부 임의의 리소스에 대한 AAD 액세스 토큰을 보냈으며 해당 토큰을 사용하여 Azure Data Explorer 리소스에 대한 새 AAD 액세스 토큰을 획득하여 응용 프로그램이 원래 AAD 액세스 토큰으로 표시된 주 서버를 대신하여 Kusto에 액세스할 수 있도록 합니다.

이 흐름을 [OAuth2 토큰 교환 흐름이라고](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)합니다.
일반적으로 AAD를 사용 하 여 여러 구성 단계가 필요 하 고 경우에 따라 (AAD 테넌트 구성에 따라) AAD 테넌트의 관리자의 특별 한 동의가 필요할 수 있습니다.



**1단계: 응용 프로그램과 Azure 데이터 탐색기 서비스 간의 신뢰 관계 설정**

1. Azure [포털을](https://portal.azure.com/) 열고 올바른 테넌트에 로그인했는지 확인합니다(포털에 로그인하는 데 사용되는 ID의 위쪽/오른쪽 모서리 참조).

2. 리소스 창에서 Azure **Active Directory를** **클릭한 다음 앱 등록을 클릭합니다.**

3. 대신 흐름을 사용하는 응용 프로그램을 찾아 엽니다.

4. **API 권한을 클릭한**다음 **권한 추가.**

5. **Azure 데이터 탐색기라는** 응용 프로그램을 검색하고 선택합니다.

6. **user_impersonation 선택 / 액세스 쿠스토**.

7. **권한 추가를 클릭합니다.**

**2단계: 서버 코드에서 토큰 교환 수행**

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

**3 단계 : Kusto 클라이언트 라이브러리에 토큰을 제공하고 쿼리를 실행합니다.**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}", 
    clusterName, 
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>웹 클라이언트(자바스크립트) 인증 및 권한 부여



**AAD 애플리케이션 구성**

> [!NOTE]
> AAD 앱을 설정하기 위해 따라야 하는 표준 [단계](./how-to-provision-aad-app.md) 외에도 AAD 응용 프로그램에서 oauth 암시적 흐름을 사용하도록 설정해야 합니다. azure 포털의 응용 프로그램 페이지에서 매니페스트를 선택하고 oauth2AllowImplicitFlow를 true로 설정하여 이를 달성할 수 있습니다.

**세부 정보**

클라이언트가 사용자의 브라우저에서 실행되는 JavaScript 코드인 경우 암시적 권한 부여 흐름이 사용됩니다. 클라이언트 응용 프로그램에 Azure Data Explorer 서비스에 대한 액세스 권한을 부여하는 토큰은 URI 조각에서 리디렉션 URI의 일부로 성공적인 인증 직후에 제공됩니다. 이 흐름에는 새로 고침 토큰이 제공되지 않으므로 클라이언트는 장기간 토큰을 캐시하고 다시 사용할 수 없습니다.

네이티브 클라이언트 흐름과 마찬가지로 두 개의 AAD 응용 프로그램(서버 및 클라이언트)이 서로 구성된 관계가 있어야 합니다. 

AdalJs는 access_token 호출이 이루어지기 전에 id_token 받아야 합니다.

액세스 토큰은 `AuthenticationContext.login()` 메서드를 호출하여 가져오고 access_tokens 호출하여 `Authenticationcontext.acquireToken()`가져옵니다.

* 올바른 구성으로 인증 컨텍스트 만들기:

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* 로그인하지 않은 `acquireToken()` 경우 호출하기 전에 호출합니다. `authContext.login()` 로그인했는지 아닌지 알 수 있는 좋은 방법은 전화하여 `authContext.getCachedUser()` 반환되는지 `false`확인하는 것입니다. )
* 페이지가 로드될 때마다 전화를 걸 `authContext.handleWindowCallback()` 수 있습니다. 이것은 AAD에서 리디렉션을 가로채서 조각 URL에서 토큰을 끌어내어 캐시하는 코드 조각입니다.
* 유효한 `authContext.acquireToken()` ID 토큰이 있으므로 실제 액세스 토큰을 가져오려면 호출합니다. acquireToken을 획득 하는 첫 번째 매개 변수는 Kusto 서버 AAD 응용 프로그램 리소스 URL 됩니다.  

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* 콜백ThatUses토큰은 Azure 데이터 탐색기 요청에서 토큰을 보유자 토큰으로 사용할 수 있습니다. 예:

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

> 경고 - 인증 할 때 다음과 같은 또는 유사한 예외가 발생하는 경우 :`ReferenceError: AuthenticationContext is not defined` 
전역 네임스페이스에 인증컨텍스트가 없기 때문일 수 있습니다. 안타깝게도 AdalJS는 현재 인증 컨텍스트가 전역 네임스페이스에 정의될 것이라는 문서화되지 않은 요구 사항을 가지고 있습니다.