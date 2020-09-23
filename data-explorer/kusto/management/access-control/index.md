---
title: Kusto 액세스 제어 개요 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 Kusto 액세스 제어 개요에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: 7b97d62e007b5294bf776fb5d5adcbac435056ef
ms.sourcegitcommit: 3fc8e9b6a313a863916031d4beba84123edcf123
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847856"
---
# <a name="kusto-access-control-overview"></a>Kusto 액세스 제어 개요

Azure Data Explorer의 Access Control은 두 가지 주요 요인을 기반으로 합니다.
* [인증](#authentication): 요청하는 보안 주체의 ID를 확인합니다.
* [권한 부여](#authorization): 요청하는 보안 주체가 대상 리소스에서 해당 요청을 수행할 수 있는지 확인합니다.

Azure Data Explorer 클러스터, 데이터베이스 또는 테이블의 쿼리 또는 제어 명령은 인증 및 권한 부여 검사를 모두 통과해야 합니다.

## <a name="authentication"></a>인증

**Azure AD(Azure Active Directory)** 는 Azure에서 선호하는 다중 테넌트 클라우드 디렉터리 서비스입니다. 보안 주체를 인증하거나 다른 ID 공급자와 페더레이션할 수 있습니다.

Azure AD는 Microsoft에서 Azure Data Explorer를 인증하는 데 선호되는 방법입니다. 지원하는 여러 인증 시나리오는 다음과 같습니다.
* **사용자 인증**(대화형 로그인): 사용자 주체를 인증하는 데 사용됩니다.
* **애플리케이션 인증**(비대화형 로그인): 사용자가 없는 상태에서 실행 및 인증해야 하는 서비스 및 애플리케이션을 인증하는 데 사용됩니다.

### <a name="user-authentication"></a>사용자 인증

사용자가 다음에 대한 자격 증명을 제공하면 사용자 인증이 수행됩니다.
* Azure AD 
* Azure AD와 작동하는 ID 공급자

성공하면 사용자는 Azure Data Explorer 서비스에 제공할 수 있는 보안 토큰을 받습니다. Azure Data Explorer 서비스는 보안 토큰을 얻는 방법을 고려하지 않습니다. 토큰이 유효한지 여부와 Azure AD(또는 페더레이션된 IdP)가 어떤 정보를 넣는지에 대해서만 고려합니다.

클라이언트 측에서 Azure Data Explorer는 Microsoft 인증 라이브러리 또는 유사한 코드가 사용자에게 자격 증명을 입력하도록 요청하는 대화식 인증을 지원합니다. 또한 Azure Data Explorer를 사용하는 애플리케이션에서 유효한 사용자 토큰을 가져오는 토큰 기반 인증도 지원합니다. Azure Data Explorer를 사용하는 애플리케이션은 다른 서비스에 대한 유효한 사용자 토큰을 얻을 수도 있습니다. 해당 리소스와 Azure Data Explorer 간에 트러스트 관계가 있는 경우에만 사용자 토큰을 얻을 수 있습니다.

Kusto 클라이언트 라이브러리를 사용하고 Azure AD를 Azure Data Explorer에 사용하여 인증하는 방법에 대한 자세한 내용은 [Kusto 연결 문자열](../../api/connection-strings/kusto.md)을 참조하세요.

### <a name="application-authentication"></a>애플리케이션 인증

요청이 특정 사용자와 연결되지 않거나 자격 증명을 입력할 수 있는 사용자가 없는 경우 Azure AD 애플리케이션 인증 흐름을 사용하세요. 이 흐름에서 애플리케이션은 몇 가지 비밀 정보를 제공하여 Azure AD(또는 페더레이션된 IdP)에 인증합니다. 다양한 Azure Data Explorer 클라이언트에서 지원하는 시나리오는 다음과 같습니다.

* 로컬로 설치된 X.509v2 인증서를 사용하는 애플리케이션 인증입니다.
* 클라이언트 라이브러리에 바이트 스트림으로 제공된 X.509v2 인증서를 사용하는 애플리케이션 인증입니다.
* Azure AD 애플리케이션 ID 및 Azure AD 애플리케이션 키를 사용하는 애플리케이션 인증입니다.

    > [!NOTE] 
    > ID와 키는 사용자 이름과 암호에 해당합니다.

* 이전에 Azure Data Explorer에 발급되어 가져온 유효한 Azure AD 토큰을 사용하는 애플리케이션 인증입니다.
* 이전에 일부 다른 리소스에 발급되어 가져온 유효한 Azure AD 토큰을 사용하는 애플리케이션 인증입니다. 이 방법은 해당 리소스와 Azure Data Explorer 간에 트러스트 관계가 있는 경우 작동합니다.

### <a name="microsoft-accounts-msas"></a>MSA(Microsoft 계정)

MSA(Microsoft 계정)는 조직 이외의 모든 Microsoft 관리형 사용자 계정에 대한 용어입니다(예: `hotmail.com`, `live.com`, `outlook.com`).
Kusto는 UPN(사용자 계정 이름)으로 식별되는 MSA(보안 그룹 개념이 없음)에 대한 사용자 인증을 지원합니다.

MSA 보안 주체가 Azure Data Explorer 리소스에 구성된 경우 Azure Data Explorer는 제공된 UPN을 확인하지 **않습니다**.

### <a name="authenticated-sdk-or-rest-calls"></a>인증된 SDK 또는 REST 호출

* REST API를 사용하는 경우 표준 HTTP `Authorization` 헤더를 사용하여 인증을 수행합니다.
* Azure Data Explorer .NET 라이브러리를 사용하는 경우 [연결 문자열](../../api/connection-strings/kusto.md)에서 인증 방법 및 매개 변수를 지정하여 인증을 제어합니다. 또 다른 방법은 [클라이언트 요청 속성](../../api/netfx/request-properties.md) 개체에 대한 속성을 설정하는 것입니다.

### <a name="azure-data-explorer-client-sdk-as-an-azure-ad-client-application"></a>Azure AD 클라이언트 애플리케이션으로서의 Azure Data Explorer 클라이언트 SDK

Kusto 클라이언트 라이브러리는 Microsoft 클라이언트 라이브러리를 호출하여 Kusto와 통신하기 위한 토큰을 획득할 때 다음 정보를 제공합니다.

* 리소스(클러스터 URI, 예: `https://Cluster-and-region.kusto.windows.net`)
* Azure AD 클라이언트 애플리케이션 ID
* Azure AD 클라이언트 애플리케이션 리디렉션 URI
* Azure AD 테넌트는 인증에 사용되는 Azure AD 엔드포인트에 영향을 줍니다. 예를 들어 Azure AD 테넌트 `microsoft.com`의 경우 Azure AD 엔드포인트는 `https://login.microsoftonline.com/microsoft.com`입니다.)

Microsoft 인증 라이브러리에서 Azure Data Explorer 클라이언트 라이브러리로 반환된 토큰에는 대상으로 적절한 Azure Data Explorer 클러스터 URL이 있고, 범위로 "Azure Data Explorer 액세스" 권한이 있습니다.

**예: Azure Data Explorer 클러스터용 Azure AD 사용자 토큰 얻기**

```csharp
// Create Auth Context for Azure AD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD TenantID or name}");

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

인증된 모든 보안 주체는 Azure Data Explorer 리소스에 대한 작업을 수행하기 전에 권한 부여 검사를 받습니다.
Azure Data Explorer는 [역할 기반 권한 부여 모델](role-based-authorization.md)을 사용하며, 여기서 보안 주체는 하나 이상의 보안 역할로 간주됩니다. 보안 주체 역할 중 하나가 승인되면 성공적으로 권한이 부여됩니다.

예를 들어, 데이터베이스 사용자 역할은 다음을 수행할 수 있는 권한을 보안 사용자, 사용자 또는 서비스에 부여합니다.
* 특정 데이터베이스의 데이터 읽기
* 데이터베이스에서 테이블 만들기
* 데이터베이스에서 함수 만들기

보안 주체와 보안 역할의 연결은 개별적으로 정의하거나 Azure AD에서 정의된 보안 그룹을 사용하여 정의할 수 있습니다. 명령은 [역할 기반 권한 부여 규칙 설정](../security-roles.md)에서 정의됩니다.
