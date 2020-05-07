---
title: Kusto 액세스 제어 개요 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 Kusto 액세스 제어 개요에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: ecdcdf22fe25c855045d90e294597c1abc769c03
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862108"
---
# <a name="kusto-access-control-overview"></a>Kusto 액세스 제어 개요

Kusto의 액세스 제어는 다음 두 차원을 기반으로 합니다.
* [인증](#authentication): 요청하는 보안 주체의 ID를 확인합니다.
* [권한 부여](#authorization): 요청하는 보안 주체가 대상 리소스에서 해당 요청을 수행할 수 있는지 확인합니다.

Kusto 클러스터, 데이터베이스 또는 테이블에서 쿼리 또는 제어 명령을 성공적으로 실행하려면 행위자에서 인증 및 권한 부여를 모두 성공적으로 통과해야 합니다.

## <a name="authentication"></a>인증


**AAD(Azure Active Directory)** 는 보안 주체를 인증하거나 Microsoft의 Active Directory와 같은 다른 ID 공급자와 페더레이션할 수 있는 Azure의 기본 설정 다중 테넌트 클라우드 디렉터리 서비스입니다.

AAD는 Microsoft에서 Kusto를 인증하는 데 기본적으로 설정되는 방법입니다. 지원하는 여러 인증 시나리오는 다음과 같습니다.
* **사용자 인증**(대화형 로그온): 사용자 주체를 인증하는 데 사용됩니다.
* **애플리케이션 인증**(비대화형 로그온): 사용자가 없는 상태에서 실행/인증해야 하는 서비스 및 애플리케이션을 인증하는 데 사용됩니다.

### <a name="user-authentication"></a>사용자 인증
사용자 인증은 사용자가 AAD(또는 ADFS와 같은 AAD와 함께 작동하는 일부 ID 공급자)에 자격 증명을 제공하고 Kusto 서비스에 제공할 수 있는 보안 토큰을 받을 때 발생합니다. Kusto 서비스는 보안 토큰을 얻는 방법에는 관심이 없지만, 토큰이 유효한지 여부와 AAD(또는 페더레이션된 IdP)에서 제공하는 정보에 관심이 있습니다.

클라이언트 쪽에서 Kusto는 AAD 클라이언트 라이브러리 ADAL 또는 이와 비슷한 코드에서 사용자에게 자격 증명을 입력하도록 요청하는 대화형 인증을 모두 지원합니다. 또한 Kusto를 사용하는 애플리케이션에서 유효한 사용자 토큰을 가져와서 표시하는 토큰 기반 인증도 지원합니다. 마지막으로, 이 기능은 해당 리소스와 Kusto 사이에 트러스트 관계가 있는 경우 Kusto를 사용하는 애플리케이션에서 Kusto가 아닌 다른 서비스에 대해 유효한 사용자 토큰을 가져오는 시나리오를 지원합니다.

Kusto 클라이언트 라이브러리를 사용하고 AAD를 Kusto에 사용하여 인증하는 방법에 대한 자세한 내용은 [Kusto 연결 문자열](../../api/connection-strings/kusto.md)을 참조하세요.

### <a name="application-authentication"></a>애플리케이션 인증
요청이 특정 사용자와 연결되지 않거나 자격 증명을 입력할 수 있는 사용자가 없는 경우 AAD 애플리케이션 인증 흐름을 사용할 수 있습니다. 이 흐름에서 애플리케이션은 몇 가지 비밀 정보를 제공하여 AAD(또는 페더레이션된 IdP)에 인증합니다. 다양한 Kusto 클라이언트에서 지원하는 시나리오는 다음과 같습니다.

* 로컬로 설치된 X.509v2 인증서를 사용하는 애플리케이션 인증입니다.
* 클라이언트 라이브러리에 바이트 스트림으로 제공된 X.509v2 인증서를 사용하는 애플리케이션 인증입니다.
* AAD 애플리케이션 ID 및 AAD 애플리케이션 키를 사용하는 애플리케이션 인증입니다(애플리케이션의 사용자 이름/암호 인증과 동일).
* 이전에 가져온 유효한 AAD 토큰(Kusto에 발급됨)을 사용하는 애플리케이션 인증입니다.
* 해당 리소스와 Kusto 사이에 트러스트 관계가 있는 경우 다른 리소스에 발급되었고 이전에 가져온 유효한 AAD 토큰을 사용하는 애플리케이션 인증입니다.


### <a name="microsoft-accounts-msas"></a>MSA(Microsoft 계정)
MSA(Microsoft 계정)는 조직 이외의 모든 Microsoft 관리형 사용자 계정에 대한 용어입니다(예: `hotmail.com`, `live.com`, `outlook.com`).
Kusto는 UPN(범용 사용자 이름)으로 식별되는 MSA(보안 그룹 개념이 없음)에 대한 사용자 인증을 지원합니다.
MSA 주체가 Kusto 리소스에 구성된 경우 Kusto는 제공된 UPN을 확인하려고 시도하지 **않습니다**.

### <a name="authenticated-sdk-or-rest-calls"></a>인증된 SDK 또는 REST 호출
* REST API를 사용하는 경우 표준 HTTP `Authorization` 헤더를 사용하여 인증을 수행합니다.
* Kusto .NET 라이브러리를 사용하는 경우 [Kusto 연결 문자열](../../api/connection-strings/kusto.md)에서 인증 방법 및 매개 변수를 지정하거나 [클라이언트 요청 속성](https://kusto.azurewebsites.net/docs/api/request-properties.html) 개체에서 속성을 설정하여 인증을 제어합니다.

### <a name="kusto-client-sdk-as-an-aad-client-application"></a>AAD 클라이언트 애플리케이션으로서의 Kusto 클라이언트 SDK
Kusto 클라이언트 라이브러리는 ADAL(AAD 클라이언트 라이브러리)을 호출하여 Kusto와 통신하기 위한 토큰을 획득할 때 다음 정보를 제공합니다.

1. 리소스(클러스터 URI, 예: `https://Cluster-and-region.kusto.windows.net`)
2. AAD 클라이언트 애플리케이션 ID
3. AAD 클라이언트 애플리케이션 리디렉션 URI
4. AAD 테넌트(이는 인증에 사용되는 AAD 엔드포인트에 영향을 미침, 예를 들어 `microsoft.com` AAD 테넌트의 경우 AAD 엔드포인트는 `https://login.microsoftonline.com/microsoft.com`임)

ADAL에서 Kusto 클라이언트 라이브러리에 반환한 토큰에는 적절한 Kusto 클러스터 URL이 대상 그룹으로, "Kusto 액세스" 권한이 범위로 포함됩니다.

**예제: Kusto 클러스터에 대한 AAD 사용자 토큰 가져오기**
```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD TenantID or name}");

// Provide your Application ID and redirect URI
var clientAppID = "{your client app id}";
var redirectUri = new Uri("{your client app redirect uri}");

// acquireTokenTask will receive the bearer token for the authenticated user
var acquireTokenTask = authContext.AcquireTokenAsync(
    $"https://{clusterNameAndRegion}.kusto.windows.net",
    clientAppID,
    redirectUri,
    new PlatformParameters(PromptBehavior.Auto, null)).GetAwaiter().GetResult();
```


## <a name="authorization"></a>권한 부여

인증에 사용되는 방법에 관계없이 인증된 모든 주체도 Kusto 리소스에서 작업을 수행하도록 허용되기 전에 권한 부여 검사를 받습니다.
Kusto는 [역할 기반 권한 부여 모델](role-based-authorization.md)을 사용합니다. 주체는 하나 이상의 **보안 역할**에 속하며, 주체의 역할 중 하나에 권한이 부여되면 해당 권한 부여가 성공합니다.

예를 들어 **데이터베이스 사용자 역할**은 특정 데이터베이스의 데이터를 읽고, 테이블을 데이터베이스에 만들고, 함수를 데이터베이스에 만들 수 있는 권한을 보안 주체(사용자 또는 서비스)에 부여합니다.

보안 주체와 보안 역할의 연결은 개별적으로 정의하거나 보안 그룹(AAD에 정의됨)을 사용하여 정의할 수 있습니다. 이러한 작업을 수행하는 개별 명령은 [역할 기반 권한 부여 규칙 설정](../security-roles.md)에 정의되어 있습니다.
