---
title: Azure 데이터 탐색기에서 Azure AD 응용 프로그램 만들기
description: Azure 데이터 탐색기에서 Azure AD 응용 프로그램을 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: 5cffee705c6a9225112e7ada8154084de40035c4
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875024"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 Azure Active Directory 응용 프로그램 등록 만들기

Azure Active Directory (Azure AD) 응용 프로그램 인증은 사용자가 없는 Azure 데이터 탐색기에 액세스 해야 하는 무인 서비스 또는 예약 된 흐름과 같은 응용 프로그램에 사용 됩니다. 웹 앱과 같은 응용 프로그램을 사용 하 여 Azure 데이터 탐색기 데이터베이스에 연결 하는 경우 서비스 주체 인증을 사용 하 여 인증 해야 합니다. 이 문서에서는 Azure AD 서비스 주체를 만들고 등록 한 다음 Azure 데이터 탐색기 데이터베이스에 액세스 하도록 권한을 부여 하는 방법에 대해 자세히 설명 합니다.

## <a name="create-azure-ad-application-registration"></a>Azure AD 응용 프로그램 등록 만들기

Azure AD 응용 프로그램 인증을 사용 하려면 Azure AD에 응용 프로그램을 만들고 등록 해야 합니다. 서비스 주체는 Azure AD 테 넌 트에서 응용 프로그램 등록을 만들 때 자동으로 생성 됩니다. 

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 블레이드를 엽니다. `Azure Active Directory`

    ![포털 메뉴에서 Azure Active Directory 선택](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. **앱 등록** 블레이드를 선택 하 고 **새 등록** 을 선택 합니다.

    ![새 앱 등록 시작](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. 다음 정보를 입력합니다. 

    * **이름** 
    * **지원 되는 계정 유형**
    * **리디렉션 URI**  >  **웹**
        > [!IMPORTANT] 
        > 응용 프로그램 유형은 **웹**이어야 합니다. URI는 선택 사항이 며이 경우 비어 있습니다.
    * **등록**을 선택합니다.

    ![새 앱 등록 등록](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. **개요** 블레이드를 선택 하 고 **응용 프로그램 ID**를 복사 합니다.

    > [!NOTE]
    > 데이터베이스에 액세스할 수 있도록 서비스 사용자에 게 권한을 부여 하려면 응용 프로그램 ID가 필요 합니다.

    ![앱 등록 ID 복사](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. **인증서 & 비밀** 블레이드에서 **새 클라이언트 암호** 를 선택 합니다.

    ![클라이언트 암호 만들기 시작](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > 이 문서에서는 응용 프로그램의 자격 증명에 클라이언트 암호를 사용 하는 방법을 설명 합니다.  X509 인증서를 사용 하 여 응용 프로그램을 인증할 수도 있습니다. **인증서 업로드** 를 선택 하 고 지침에 따라 인증서의 공개 부분을 업로드 합니다.

1. 설명, 만료를 입력 하 고 **추가** 를 선택 합니다.

    ![클라이언트 비밀 매개 변수 입력](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. 키 값을 복사합니다.

    > [!NOTE]
    > 이 페이지를 벗어나면 키 값에 액세스할 수 없습니다.  데이터베이스에 대 한 클라이언트 자격 증명을 구성 하려면 키가 필요 합니다.

    ![클라이언트 비밀 키 값 복사](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

응용 프로그램이 만들어집니다. 아래의 프로그래밍 예제에서와 같이 권한 있는 Azure 데이터 탐색기 리소스에만 액세스 해야 하는 경우 다음 섹션을 건너뜁니다. 위임 된 권한 지원은 [응용 프로그램 등록에 대 한 위임 된 권한 구성](#configure-delegated-permissions-for-the-application-registration)을 참조 하세요.

## <a name="configure-delegated-permissions-for-the-application-registration"></a>응용 프로그램 등록에 대 한 위임 된 권한 구성

응용 프로그램에서 호출 하는 사용자의 자격 증명을 사용 하 여 Azure 데이터 탐색기에 액세스 해야 하는 경우 응용 프로그램 등록에 대 한 위임 된 권한을 구성 합니다. 예를 들어 Azure 데이터 탐색기에 액세스 하는 web API를 빌드하고 API를 *호출* 하는 사용자의 자격 증명을 사용 하 여 인증 하려는 경우를 예로 들 수 있습니다.  

1. **API 권한** 블레이드에서 **사용 권한 추가**를 선택 합니다.
1. **내 조직에서 사용 하는 api**를 선택 합니다. **Azure 데이터 탐색기**을 검색 하 고 선택 합니다.

    ![Azure 데이터 탐색기 API 권한 추가](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. **위임 된 권한**에서 **user_impersonation** 상자를 선택 하 고 **사용 권한을 추가** 합니다.

    ![사용자 가장을 사용 하 여 위임 된 권한 선택](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>서비스 사용자에 게 Azure 데이터 탐색기 데이터베이스에 대 한 액세스 권한 부여

이제 서비스 주체 응용 프로그램 등록을 만들었으므로 Azure 데이터 탐색기 데이터베이스에 대 한 해당 서비스 주체 액세스 권한을 부여 해야 합니다. 

1. [웹 UI](https://dataexplorer.azure.com/)에서 데이터베이스에 연결 하 고 쿼리 탭을 엽니다.

1. 다음 명령을 실행합니다.

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    예를 들면
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    마지막 매개 변수는 데이터베이스와 연결 된 역할을 쿼리할 때 메모로 표시 되는 문자열입니다.
    
    > [!NOTE]
    > 응용 프로그램 등록을 만든 후 Azure 데이터 탐색기에서 참조할 수 있을 때까지 몇 분 지연 될 수 있습니다. 명령을 실행할 때 응용 프로그램을 찾을 수 없다는 오류가 표시 되 면 기다렸다가 다시 시도 합니다.

자세한 내용은 [보안 역할 관리](kusto/management/security-roles.md) 및 수집 [권한](kusto/api/netfx/kusto-ingest-client-permissions.md)을 참조 하세요.  

## <a name="using-application-credentials-to-access-a-database"></a>응용 프로그램 자격 증명을 사용 하 여 데이터베이스 액세스

응용 프로그램 자격 증명을 사용 하 여 [Azure 데이터 탐색기 클라이언트 라이브러리](kusto/api/netfx/about-kusto-data.md)를 통해 프로그래밍 방식으로 데이터베이스에 액세스 합니다.

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
   > 이전에 만든 응용 프로그램 등록 (서비스 사용자)의 응용 프로그램 id 및 키를 지정 합니다.

자세한 내용은 Azure를 사용 [하 여 AZURE AD 인증 데이터 탐색기](kusto/management/access-control/how-to-authenticate-with-aad.md) [.net Core 웹 앱을 사용 하 여 Azure Key Vault](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)액세스 및 사용을 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="invalid-resource-error"></a>잘못 된 리소스 오류

응용 프로그램을 사용 하 여 Azure 데이터 탐색기 액세스용 사용자 또는 응용 프로그램을 인증 하는 경우 Azure 데이터 탐색기 서비스 응용 프로그램에 대 한 위임 된 권한을 설정 해야 합니다. 응용 프로그램을 선언 하 여 Azure 데이터 탐색기 액세스를 위해 사용자 또는 응용 프로그램을 인증할 수 있습니다. 이렇게 하지 않으면 인증 시도가 수행 될 때 다음과 유사한 오류가 발생 합니다.

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

[Azure 데이터 탐색기 서비스 응용 프로그램에 대 한 위임 된 권한 설정](#configure-delegated-permissions-for-the-application-registration)에 대 한 지침을 따라야 합니다.

### <a name="enable-user-consent-error"></a>사용자 동의 사용 오류

Azure AD 테 넌 트 관리자는 테 넌 트 사용자가 응용 프로그램에 동의 하지 못하도록 하는 정책을 시행할 수 있습니다. 이 경우 사용자가 응용 프로그램에 로그인 하려고 하면 다음과 같은 오류가 발생 합니다.

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

테 넌 트의 모든 사용자에 게 동의를 부여 하거나 특정 응용 프로그램에 대 한 사용자 동의를 사용 하도록 설정 하려면 Azure AD 관리자에 게 문의 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 지원 되는 연결 문자열 목록은 [Kusto 연결 문자열](kusto/api/connection-strings/kusto.md) 을 참조 하세요.
