---
title: 보안 주체 및 ID 공급자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 보안 주체 및 ID 공급자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0638ba0031162dadbb4b9a2815940e66d4dcfb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522647"
---
# <a name="principals-and-identity-providers"></a>보안 주체 및 ID 공급자

Kusto 권한 부여 모델은 여러 ID 공급자(IdP) 및 여러 주체 유형을 지원합니다.
이 문서에서는 지원되는 보안 주체 유형을 검토하고 역할 할당 명령을 사용하여 사용하는 것을 보여 [줍니다.](../../management/security-roles.md)

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory(AAD)는 Azure가 선호하는 다중 테넌트 클라우드 디렉터리 서비스 및 ID 공급자로, 보안 주체를 인증하거나 Microsoft의 Active Directory와 같은 다른 ID 공급자와 페더레이션할 수 있습니다.

AAD는 Kusto에 인증하는 데 선호되는 방법입니다. 지원하는 여러 인증 시나리오는 다음과 같습니다.
* **사용자 인증**(대화형 로그온): 사용자 주체를 인증하는 데 사용됩니다.
* **애플리케이션 인증**(비대화형 로그온): 사용자가 없는 상태에서 실행/인증해야 하는 서비스 및 애플리케이션을 인증하는 데 사용됩니다.

>참고: Azure Active Directory는 서비스 계정의 인증을 허용하지 않습니다(예전에 대한 AD 엔터티정의).
AD 서비스 계정과 동등한 AAD 는 AAD 응용 프로그램입니다.

#### <a name="aad-group-principals"></a>AAD 그룹 주체
Kusto는 보안 그룹 보안 주체만 지원합니다(메일 그룹 보안 서버는 아님). Kusto 클러스터에서 DG에 대한 액세스를 설정하려고 하면 오류가 발생합니다.

#### <a name="aad-tenants"></a>AAD 테넌스


>AAD 테넌트가 명시적으로 지정되지 않은 경우 Kusto는 제공된 경우 UPN(유니버설 PrincipalName, 예: `johndoe@fabrikam.com`)에서 이를 해결하려고 시도합니다.
주체가 UPN 양식이 아닌 테넌트 정보를 포함하지 않는 경우 테넌트 ID 또는 이름을 주 설명자에 부가하여 명시적으로 언급해야 합니다.

**AAD 보안 주체에 대한 예**

|AAD 테넌트 |Type |구문 |
|-----------|-----|-------|
|암시적(UPN)  |사용자  |`aaduser=`*사용자 이메일 주소*
|명시적(ID)   |사용자  |`aaduser=`*사용자 이메일 주소*`;` `aaduser=`*테넌트 ID* 또는 *ObjectID*`;`*테넌트ID*
|명시적(이름) |사용자  |`aaduser=`*사용자 이메일 주소*`;` `aaduser=`*테넌트 이름* 또는 *ObjectID*`;`*테넌트 이름*
|암시적(UPN)  |그룹 |`aadgroup=`*그룹이메일주소*
|명시적(ID)   |그룹 |`aadgroup=`*GroupObjectId*`;`*테넌트 Id* 또는`aadgroup=`*그룹표시 이름*`;`*테넌트ID*
|명시적(이름) |그룹 |`aadgroup=`*GroupObjectId*`;`*테넌트 이름* 또는`aadgroup=`*그룹표시 이름*`;`*테넌트 이름*
|명시적(UPN)  |앱   |`aadapp`=*응용 프로그램 표시 이름*`;`*테넌트ID*
|명시적(이름) |앱   |`aadapp=`*응용 프로그램 ID*`;`*테넌트 이름*

```kusto
// No need to specify AAD tenant for UPN, as Kusto performs the resolution by itself
.add database Test users ('aaduser=imikeoein@fabrikam.com') 'Test user (AAD)'

// AAD SG on 'fabrikam.com' tenant
.add database Test users ('aadgroup=SGDisplayName;fabrikam.com') 'Test group @fabrikam.com (AAD)'

// AAD App on 'fabrikam.com' tenant - by tenant name
.add database Test users ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com') 'Test app @fabrikam.com (AAD)'
```

### <a name="microsoft-accounts-msas"></a>MSA(Microsoft 계정)
MSA(Microsoft 계정)는 조직 이외의 모든 Microsoft 관리형 사용자 계정에 대한 용어입니다(예: `hotmail.com`, `live.com`, `outlook.com`).
Kusto는 UPN(범용 사용자 이름)으로 식별되는 MSA(보안 그룹 개념이 없음)에 대한 사용자 인증을 지원합니다.
MSA 주체가 Kusto 리소스에 구성된 경우 Kusto는 제공된 UPN을 확인하려고 시도하지 **않습니다**.

**MSA 보안 주체에 대한 예제**

|IdP  |Type  |구문 |
|-----|------|-------|
|Live.com |사용자  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

