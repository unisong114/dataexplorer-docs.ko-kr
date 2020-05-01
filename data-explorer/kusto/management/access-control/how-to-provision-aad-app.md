---
title: 방법-AAD 응용 프로그램 만들기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 AAD 응용 프로그램을 방법 하는 방법에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 0816d55cda6d29820084514b0bfec27d726693f9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617972"
---
# <a name="howto-creating-an-aad-application"></a>방법: AAD 응용 프로그램 만들기

Aad 사용자 인증은 매우 간단 하지만 (예: MSIT와 같은 테 넌 트 관리자가 AAD에서 사용자를 정의 함) AAD 응용 프로그램 인증은 다소 복잡 합니다. 이는 AAD를 사용 하 여 응용 프로그램을 만들고 등록 하는 데 필요 하기 때문입니다. 이 프로세스는 아래에 자세히 설명 되어 있습니다.

AAD 응용 프로그램 인증은 사용자가 로그온 하거나 제공 (예: 무인 서비스 또는 예약 된 흐름) 하지 않고도 Kusto에 액세스 해야 하는 응용 프로그램에 유용 합니다.

클라이언트 응용 프로그램 및 대화형 사용자 컨텍스트가 있는 중간 계층 응용 프로그램은이 모델을 피해 야 합니다. 이는 권한 부여가 사용자 id 대신 AAD 응용 프로그램 id를 기반으로 수행 되기 때문입니다. 따라서 호출 응용 프로그램은 오용을 방지 하기 위해 자체 권한 부여 논리를 구현 해야 합니다.

## <a name="application-authentication-use-cases"></a>응용 프로그램 인증 사용 사례

응용 프로그램 인증을 사용 하는 두 가지 주요 시나리오는 다음과 같습니다.
* Kusto 서비스에 직접 연결 하 여 직접 연결 하는 응용 프로그램
* Kusto (위임 된 인증)에 대 한 사용자를 인증 하는 응용 프로그램

## <a name="1-provisioning-a-new-application"></a>1. 새 응용 프로그램 프로 비전

#### <a name="register-the-new-application"></a>새 응용 프로그램 등록

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 블레이드를 `Azure Active Directory` 엽니다.

    :::image type="content" source="images/aad-create-app-step-0.png" alt-text="Aad 앱 만들기 단계 0":::

1. 블레이드가 `App registrations` 로드 될 때를 선택 하 고 `New application registration`를 선택 합니다.

    :::image type="content" source="images/aad-create-app-step-1.png" alt-text="Aad 앱 만들기 단계 1":::

1. 응용 프로그램 세부 정보를 입력 합니다.
    * 속성
    * 형식:로 설정 합니다.`Web app/API`
    * 로그온 URL: 사용자가 응용 프로그램에 액세스 하는 데 사용 하는 URL입니다. AAD는 URL의 유효성을 검사 하지 않습니다.<br>
        하지만 사용자에 게 값을 제공 하도록 요구 합니다. 따라서 사용자가 응용 프로그램에 액세스할 수 있는 URL이 없으면<br>
        응용 프로그램에 속하는 URL (예: https://<APP-V> 또는 https://> <cloudapp.net)을 입력 합니다.<br>
        응용 프로그램이 아직 작성 되지 않은 경우에도 값을 제공 하 고 계속할 수 있습니다.

    
    :::image type="content" source="images/aad-create-app-step-2.png" alt-text="Aad 앱 만들기 2 단계":::

1. 응용 프로그램이 준비 되 면 `Settings` 블레이드를 엽니다.

    :::image type="content" source="images/aad-create-app-step-3.png" alt-text="Aad 만들기 3 단계":::

1. `Keys` 블레이드에서 새 응용 프로그램에 대 한 인증을 설정 합니다.
    * 공유 키를 사용 하려면 드롭다운 메뉴에서 키 기간을 선택 하 고 생성 될 때 키를 복사 합니다.
        이 키는 복원할 수 없습니다.
    * 또는 X509 인증서를 사용 하 여 응용 프로그램을 인증 합니다.
        이렇게 하려면 지침을 선택 `Upload Public Key` 하 고 지침에 따라 인증서의 공개 부분을 업로드 합니다.
    * 완료 되 면 선택 `Save` 하는 것을 잊지 마세요.

    :::image type="content" source="images/aad-create-app-step-4.png" alt-text="Aad 앱 만들기 4 단계":::

응용 프로그램을 설정 합니다. 새로 만든 응용 프로그램을 사용 하 여 Kusto에 액세스 하기만 하면 됩니다.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Kusto 서비스 응용 프로그램에 대 한 위임 된 권한 설정

응용 프로그램이 Kusto 액세스를 위해 사용자 또는 앱 인증을 수행할 수 있어야 하는 경우 응용 프로그램과 Kusto 간에 트러스트를 설정 해야 합니다.

1. `Required permissions` 블레이드에서를 선택 `Add`합니다.

    :::image type="content" source="images/aad-create-app-step-5.png" alt-text="Aad 앱 만들기 5 단계":::
   
1. 을 `Select an API`선택 하 `KustoService` 고 필터 상자에를 입력 `KustoService (Kusto)`한 다음를 선택 합니다.
1. Kusto 클러스터에 MFA가 필요한 경우 필터 `KustoMFA` 상자에를 입력 하 고 `KustoServiceMFA (KustoMFA)`을 선택 합니다.

    :::image type="content" source="images/aad-create-app-step-6.png" alt-text="Aad 앱 만들기 6 단계":::

1. 선택 항목을 확인 한 후에 대 한 `Access Kusto`위임 된 권한을 선택 합니다.

   :::image type="content" source="images/aad-create-app-step-7.png" alt-text="Aad 앱 만들기 7 단계":::

1. 프로세스 `Done` 를 완료 하려면 선택 합니다.


### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Kusto cluster의 응용 프로그램에 대 한 사용 권한 설정

> 새로 프로 비전 된 응용 프로그램을 사용 하기 전에 Graph API에서 확인 되는지 확인 합니다.<br>
    AAD 변경 내용이 전파 되는 데 최대 몇 시간이 걸릴 수 있습니다.

1. 데이터베이스 관리자에 게 액세스 하 여 새 앱에 대 한 사용 권한을 부여 합니다.
권한 설정에 대 한 자세한 내용은 [데이터베이스 보안 주체 관리](../security-roles.md) 섹션을 참조 하세요.<br>
수집 권한 설정에 대 한 자세한 내용은 수집 [권한](../../api/netfx/kusto-ingest-client-permissions.md)을 참조 하세요.
1. 아직 없는 경우 Kusto 탐색기에서이 서비스에 대 한 연결을 추가 합니다.

### <a name="3-application-can-now-access-kusto"></a>3. 이제 응용 프로그램이 Kusto에 액세스할 수 있습니다.

새로 프로 비전 된 응용 프로그램을 사용 하 여 Kusto 클라이언트 라이브러리를 사용 하 여 Kusto 클러스터에 액세스 하는 경우에는 다음 연결 문자열을 지정 합니다 (공유 키로 인증 하도록 선택한 경우).

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*applicationclientid*`;Application Key=`*applicationclientid*


### <a name="appendix-a-aad-errors"></a>부록 A: AAD 오류

#### <a name="aad-error-aadsts650057"></a>AAD 오류 AADSTS650057

응용 프로그램을 사용 하 여 Kusto에 액세스할 사용자 또는 응용 프로그램을 인증 하는 경우 Kusto 서비스 응용 프로그램에 대 한 위임 된 권한을 설정 해야 합니다. 즉, 응용 프로그램이 Kusto에 액세스할 수 있는 사용자 또는 응용 프로그램을 인증할 수 있습니다.
이렇게 하지 않으면 인증 시도가 수행 될 때 다음과 유사한 오류가 발생 합니다.

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

[Kusto 서비스 응용 프로그램에 대 한 위임 된 권한을 설정](#set-up-delegated-permissions-for-kusto-service-application)하는 방법에 대 한 지침을 따라야 합니다.

#### <a name="enable-user-consent-if-needed"></a>필요한 경우 사용자 동의 사용

AAD 테 넌 트 관리자는 테 넌 트 사용자가 응용 프로그램에 동의 하지 못하도록 하는 정책을 시행할 수 있습니다. 그러면 사용자가 응용 프로그램에 로그인 하려고 할 때 다음과 같은 오류가 발생 합니다.

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

AAD 관리자에 게 테 넌 트의 모든 사용자에 게 동의를 부여 하도록 요청 하거나 특정 응용 프로그램에 대 한 사용자 동의를 사용 하도록 요청 해야 합니다.

### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>부록 B: 고급 항목 및 문제 해결

* 지원 되는 연결 문자열의 전체 참조는 [Kusto 연결 문자열](../../api/connection-strings/kusto.md) 설명서를 참조 하세요.
