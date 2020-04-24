---
title: Azure 데이터 탐색기에서 Azure AD 응용 프로그램 만들기
description: Azure 데이터 탐색기에서 Azure AD 응용 프로그램을 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 7bbb8064cd71e61b6fdfb1205e783099ec21c687
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501595"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 Azure Active 디렉터리 응용 프로그램 등록 만들기

Azure Active Directory(Azure AD) 응용 프로그램 인증은 사용자가 없는 Azure Data Explorer에 액세스해야 하는 무인 서비스 또는 예약된 흐름과 같은 응용 프로그램에 사용됩니다. 웹 앱과 같은 응용 프로그램을 사용하여 Azure Data Explorer 데이터베이스에 연결하는 경우 서비스 주체 인증을 사용하여 인증해야 합니다. 이 문서에서는 Azure AD 서비스 주체를 만들고 등록한 다음 Azure Data Explorer 데이터베이스에 액세스할 수 있는 권한을 부여하는 방법을 자세히 설명합니다.

## <a name="create-azure-ad-application-registration"></a>Azure AD 응용 프로그램 등록 만들기

Azure AD 응용 프로그램 인증을 사용하려면 Azure AD를 사용 하 여 응용 프로그램을 만들고 등록 해야 합니다. 서비스 주체는 Azure AD 테넌트에서 응용 프로그램 등록을 만들 때 자동으로 만들어집니다. 

1. [Azure 포털에](https://portal.azure.com) 로그인하고 `Azure Active Directory` 블레이드를 엽니다.

    ![포털 메뉴에서 Azure 활성 디렉터리 선택](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. 앱 등록 블레이드를 선택하고 **새 등록을** **선택합니다.**

    ![새 앱 등록 시작](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. 다음 정보를 입력합니다. 

    * **이름** 
    * **지원되는 계정 유형**
    * **URI** > **웹** 리디렉션
        > [!IMPORTANT] 
        > 응용 프로그램 유형은 **웹**이어야 합니다. URI는 선택 사항이며 이 경우 비어 있습니다.
    * **레지스터** 선택

    ![새 앱 등록](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. **개요** 블레이드를 선택하고 **응용 프로그램 ID를**복사합니다.

    > [!NOTE]
    > 서비스 주체가 데이터베이스에 액세스하도록 권한을 부여하려면 응용 프로그램 ID가 필요합니다.

    ![앱 등록 ID 복사](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. 인증서 **& 비밀** 블레이드에서 **새 클라이언트 비밀을** 선택합니다.

    ![클라이언트 비밀 만들기 시작](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > 이 문서에서는 응용 프로그램의 자격 증명에 대 한 클라이언트 비밀을 사용 하 여 설명 합니다.  X509 인증서를 사용하여 응용 프로그램을 인증할 수도 있습니다. **인증서 업로드를** 선택하고 지침에 따라 인증서의 공개 부분을 업로드합니다.

1. 설명, 만료를 입력하고 **추가를** 선택합니다.

    ![클라이언트 비밀 매개 변수 입력](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. 키 값을 복사합니다.

    > [!NOTE]
    > 이 페이지를 나가면 키 값에 액세스할 수 없습니다.  데이터베이스에 클라이언트 자격 증명을 구성하려면 키가 필요합니다.

    ![클라이언트 비밀 키 값 복사](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

응용 프로그램이 만들어집니다. 아래 프로그래밍 예제와 같이 권한이 부여된 Azure Data Explorer 리소스에만 액세스해야 하는 경우 다음 섹션을 건너뜁니다. 위임된 사용 권한 지원의 경우 [응용 프로그램 등록에 대해 위임된 사용 권한 구성을](#configure-delegated-permissions-for-the-application-registration)참조하십시오.

## <a name="configure-delegated-permissions-for-the-application-registration"></a>응용 프로그램 등록에 대해 위임된 권한 구성

응용 프로그램이 호출하는 사용자의 자격 증명을 사용하여 Azure Data Explorer에 액세스해야 하는 경우 응용 프로그램 등록에 대해 위임된 권한을 구성합니다. 예를 들어 Azure Data Explorer에 액세스하기 위해 웹 API를 빌드하는 경우 API를 *호출하는* 사용자의 자격 증명을 사용하여 인증하려는 경우  

1. API 권한 블레이드에서 **권한 추가를** **선택합니다.**
1. **조직에서 사용하는 API를**선택합니다. Azure 데이터 **탐색기를**검색하고 선택합니다.

    ![Azure 데이터 탐색기 API 권한 추가](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. **위임된 사용 권한에서** **user_impersonation** 상자를 선택하고 **사용 권한 추가**

    ![사용자 가장을 사용하여 위임된 권한 선택](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Azure 데이터 탐색기 데이터베이스에 서비스 주체 액세스 권한 부여

서비스 주체 응용 프로그램 등록이 만들어졌으면 Azure Data Explorer 데이터베이스에 해당 서비스 주체 액세스 권한을 부여해야 합니다. 

1. 웹 [UI에서](https://dataexplorer.azure.com/)데이터베이스에 연결하고 쿼리 탭을 엽니다.

1. 다음 명령을 실행하십시오.

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    다음은 그 예입니다.
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    마지막 매개 변수는 데이터베이스와 연결된 역할을 쿼리할 때 메모로 표시되는 문자열입니다.
    
    > [!NOTE]
    > 응용 프로그램 등록을 만든 후 Azure Data 탐색기가 응용 프로그램을 참조할 수 있을 때까지 몇 분 지연될 수 있습니다. 명령을 실행할 때 응용 프로그램을 찾을 수 없다는 오류가 발생하면 기다렸다가 다시 시도하십시오.

자세한 내용은 [보안 역할 관리](kusto/management/security-roles.md) 및 수집 [권한을](kusto/api/netfx/kusto-ingest-client-permissions.md)참조하십시오.  

## <a name="using-application-credentials-to-access-a-database"></a>응용 프로그램 자격 증명을 사용하여 데이터베이스에 액세스

응용 프로그램 자격 증명을 사용하여 Azure Data [Explorer 클라이언트 라이브러리를](kusto/api/netfx/about-kusto-data.md)사용하여 데이터베이스에 프로그래밍 방식으로 액세스합니다.

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > 이전에 만든 응용 프로그램 ID와 응용 프로그램 등록(서비스 주체)의 키를 지정합니다.

자세한 내용은 [Azure 데이터 탐색기 액세스에 대한 Azure AD를 사용하여 인증하고](kusto/management/access-control/how-to-authenticate-with-aad.md) [.NET Core 웹 앱에서 Azure 키 자격 증명 모음을 사용합니다.](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)

## <a name="troubleshooting"></a>문제 해결

### <a name="invalid-resource-error"></a>잘못된 리소스 오류

응용 프로그램이 Azure Data Explorer 액세스에 대한 사용자 또는 응용 프로그램을 인증하는 데 사용되는 경우 Azure Data Explorer 서비스 응용 프로그램에 대해 위임된 권한을 설정해야 합니다. 응용 프로그램이 Azure Data Explorer 액세스에 대한 사용자 또는 응용 프로그램을 인증할 수 있다고 선언합니다. 이렇게 하지 않으면 인증을 시도할 때 다음과 유사한 오류가 발생합니다.

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

[Azure Data Explorer 서비스 응용 프로그램에 대해 위임된 사용 권한 설정에 대한](#configure-delegated-permissions-for-the-application-registration)지침을 따라야 합니다.

### <a name="enable-user-consent-error"></a>사용자 동의 오류 사용

Azure AD 테넌트 관리자는 테넌트 사용자가 응용 프로그램에 동의하지 못하도록 하는 정책을 제정할 수 있습니다. 이 경우 사용자가 응용 프로그램에 로그인하려고 할 때 다음과 유사한 오류가 발생합니다.

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

테넌트의 모든 사용자에 대한 동의를 부여하거나 특정 응용 프로그램에 대한 사용자 동의를 사용하도록 설정하려면 Azure AD 관리자에게 문의해야 합니다.

## <a name="next-steps"></a>다음 단계

* 지원되는 연결 문자열 목록은 [Kusto 연결 문자열을](kusto/api/connection-strings/kusto.md) 참조하십시오.
