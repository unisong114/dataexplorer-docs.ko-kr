---
title: HowTo - AAD 응용 프로그램 만들기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 HowTo - Azure 데이터 탐색기에서 AAD 응용 프로그램 만들기에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 91d646d3bd0922f9daf9e8caec6fa6be5e32e2b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522902"
---
# <a name="howto-creating-an-aad-application"></a>방법: AAD 응용 프로그램 만들기

AAD 사용자 인증은 매우 쉽지만(사용자가 MSIT과 같은 테넌트 관리자에 의해 AAD에 정의되어 있기 때문에) AAD 응용 프로그램 인증은 다소 복잡합니다. 이는 AAD를 사용하여 응용 프로그램을 만들고 등록해야 하기 때문입니다. 이 프로세스는 몇 가지 세부 사항에 아래에 설명되어 있습니다.

AAD 응용 프로그램 인증은 사용자가 로그온하거나 존재하지 않고 Kusto에 액세스해야 하는 응용 프로그램(예: 무인 서비스 또는 예약된 흐름)에 유용합니다.

대화형 사용자 컨텍스트가 있는 클라이언트 응용 프로그램 및 중간 계층 응용 프로그램은 이 모델을 피해야 합니다. 이는 권한 부여가 사용자 ID 대신 AAD 응용 프로그램 ID를 기반으로 수행되므로 호출 응용 프로그램은 오용을 방지하기 위해 자체 권한 부여 논리를 구현해야 하기 때문입니다.

## <a name="application-authentication-use-cases"></a>응용 프로그램 인증 사용 사례

응용 프로그램 인증을 사용하는 두 가지 주요 시나리오가 있습니다.
* Kusto 서비스에 직접 직접 연락하는 응용 프로그램
* 사용자를 Kusto로 인증하는 응용 프로그램(위임된 인증)

## <a name="1-provisioning-a-new-application"></a>1. 새 응용 프로그램 프로비저닝

#### <a name="register-the-new-application"></a>새 응용 프로그램 등록

1. [Azure 포털에](https://portal.azure.com) 로그인하고 `Azure Active Directory` 블레이드를 엽니다.

    ![대체 텍스트](./images/Aad-create-app-step-0.png "Aad-create-app-step-0")

1. 블레이드가 로드될 때 `App registrations` `New application registration`선택하고 선택합니다.

    ![대체 텍스트](./images/Aad-create-app-step-1.png "Aad-create-app-step-1")

1. 응용 프로그램 세부 정보를 입력합니다.
    * 이름
    * 유형: 설정`Web app/API`
    * 사인온 URL: 사용자가 응용 프로그램에 액세스하는 데 사용하는 URL입니다. AAD는 URL의 유효성을 검사하지 않습니다.<br>
        하지만 값을 제공해야 합니다. 따라서 응용 프로그램에 사용자가 액세스할 수 있는 URL이 없는 경우<br>
        응용 프로그램에 속하는 URL을 입력합니다(예: https://<APP-CNAME> 또는 https://<클라우드 서비스 이름>.cloudapp.net).<br>
        응용 프로그램이 아직 작성되지 않은 경우 값을 제공하고 계속할 수도 있습니다.

    ![대체 텍스트](./images/Aad-create-app-step-2.png "Aad-create-app-step-2")

1. 응용 프로그램이 준비되면 블레이드를 엽니다. `Settings`

    ![대체 텍스트](./images/Aad-create-app-step-3.png "Aad-create-app-step-3")

1. 블레이드에서 `Keys` 새 응용 프로그램에 대한 인증을 설정합니다.
    * 공유 키를 사용하려면 드롭다운 메뉴에서 키 지속 시간을 선택하고 키가 생성될 때 키를 복사합니다.
        이 키는 복원할 수 없습니다.
    * 또는 X509 인증서를 사용하여 응용 프로그램을 인증합니다.
        이렇게 하려면 지침을 `Upload Public Key` 선택하고 따라 인증서의 공개 부분을 업로드합니다.
    * 작업이 완료되면 선택하는 `Save` 것을 잊지 마십시오.

    ![대체 텍스트](./images/Aad-create-app-step-4.png "Aad-create-app-step-4")

응용 프로그램이 설정되었습니다. 새로 만든 응용 프로그램으로 Kusto에 액세스하기만 하면 완료됩니다.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Kusto 서비스 응용 프로그램에 대해 위임된 권한 설정

Kusto 액세스에 대한 사용자 또는 앱 인증을 수행할 수 있는 응용 프로그램이 필요한 경우 응용 프로그램과 Kusto 간에 신뢰를 설정해야 합니다.

1. 블레이드에서 `Required permissions` 을 `Add`선택합니다.

    ![대체 텍스트](./images/Aad-create-app-step-5.png "Aad-create-app-step-5")

1. `Select an API`선택합니다, `KustoService` 필터 상자에 입력하고 을 선택합니다. `KustoService (Kusto)`
1. Kusto 클러스터에 MFA가 필요한 `KustoMFA` 경우 필터 상자에 `KustoServiceMFA (KustoMFA)`입력하고 을 선택합니다.

    ![대체 텍스트](./images/Aad-create-app-step-6.png "Aad-create-app-step-6")

1. 선택 항목을 확인한 후 에 대해 `Access Kusto`위임된 권한을 선택합니다.

    ![대체 텍스트](./images/Aad-create-app-step-7.png "Aad-create-app-step-7")

1. 프로세스를 완료하려면 선택합니다. `Done`



### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Kusto 클러스터의 응용 프로그램에 대한 사용 권한 설정

> 새로 프로비전된 응용 프로그램을 사용하기 전에 Graph API에서 해결되었는지 확인합니다.<br>
    AAD 변경 사항이 전파되는 데 최대 몇 시간이 걸릴 수 있습니다.

1. 데이터베이스 관리자에 액세스하여 새 앱에 대한 권한을 부여합니다.
사용 권한 설정에 대한 자세한 내용은 [데이터베이스 보안 주체 관리](../security-roles.md) 섹션을 참조하십시오.<br>
사용 권한 설정은 [인기 사용 권한](../../api/netfx/kusto-ingest-client-permissions.md)을 참조하십시오.
1. 아직 이 서비스가 없는 경우 Kusto Explorer에서 이 서비스에 연결을 추가합니다.

### <a name="3-application-can-now-access-kusto"></a>3. 응용 프로그램은 이제 쿠스토에 액세스 할 수 있습니다

새로 프로비전된 응용 프로그램을 사용하여 Kusto 클라이언트 라이브러리를 사용하여 Kusto 클러스터에 액세스하는 경우 다음 연결 문자열을 지정합니다(공유 키로 인증하도록 선택한 경우).

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*응용 프로그램클라이언트ID*`;Application Key=`*응용 프로그램 키*


### <a name="appendix-a-aad-errors"></a>부록 A: AAD 오류

#### <a name="aad-error-aadsts650057"></a>AAD 오류 AADSTS650057

응용 프로그램이 Kusto 액세스에 대한 사용자 또는 응용 프로그램을 인증하는 데 사용되는 경우 Kusto 서비스 응용 프로그램에 대해 위임된 권한을 설정해야 합니다.
이렇게 하지 않으면 인증을 시도할 때 다음과 유사한 오류가 발생합니다.

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

[Kusto 서비스 응용 프로그램에 대한 위임된 사용 권한 설정에 대한](#set-up-delegated-permissions-for-kusto-service-application)지침을 따라야 합니다.

#### <a name="enable-user-consent-if-needed"></a>필요한 경우 사용자 동의 사용

AAD 테넌트 관리자는 테넌트 사용자가 응용 프로그램에 동의하지 못하도록 하는 정책을 제정할 수 있습니다. 이렇게 하면 사용자가 응용 프로그램에 로그인하려고 할 때 다음과 유사한 오류가 발생합니다.

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

AAD 관리자에게 테넌트의 모든 사용자에 대한 동의를 요청하거나 특정 응용 프로그램에 대한 사용자 동의를 사용하도록 설정해야 합니다.



### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>부록 B: 고급 주제 및 문제 해결

* 지원되는 연결 문자열에 대한 전체 참조는 [Kusto 연결 문자열](../../api/connection-strings/kusto.md) 설명서를 참조하십시오.
