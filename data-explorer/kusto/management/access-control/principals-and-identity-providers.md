---
title: 보안 주체 및 Id 공급자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 보안 주체 및 Id 공급자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e34c724799cffe38db93869e96fcfae83a92b55
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664996"
---
# <a name="principals-and-identity-providers"></a>보안 주체 및 Id 공급자

Kusto 권한 부여 모델은 여러 Id 공급자 (IdPs) 및 여러 보안 주체 유형을 지원 합니다.
이 문서에서는 지원 되는 보안 주체 유형을 검토 하 고 [역할 할당 명령과](../../management/security-roles.md)함께 사용 하는 방법을 보여 줍니다.

### <a name="azure-active-directory"></a>Azure Active Directory
AAD (Azure Active Directory)는 보안 주체를 인증 하거나 Microsoft의 Active Directory 같은 다른 id 공급자와 페더레이션 할 수 있는 Azure의 선호 하는 다중 테 넌 트 클라우드 디렉터리 서비스 및 Id 공급자입니다.

AAD는 Kusto에 인증 하는 데 선호 되는 방법입니다. 지원하는 여러 인증 시나리오는 다음과 같습니다.
* **사용자 인증**(대화형 로그온): 사용자 주체를 인증하는 데 사용됩니다.
* **애플리케이션 인증**(비대화형 로그온): 사용자가 없는 상태에서 실행/인증해야 하는 서비스 및 애플리케이션을 인증하는 데 사용됩니다.

> [!NOTE]
> Azure Active Directory은 (는) 서비스 계정의 인증을 허용 하지 않습니다 (정의-프레미스 AD 엔터티).
AD 서비스 계정에 해당 하는 AAD는 AAD 응용 프로그램입니다.

#### <a name="aad-group-principals"></a>AAD 그룹 보안 주체
Kusto는 보안 그룹 보안 주체 (배포 그룹은 지원 하지 않음)만 지원 합니다. Kusto 클러스터에서 DG에 대 한 액세스를 설정 하려고 하면 오류가 발생 합니다.

#### <a name="aad-tenants"></a>AAD 테 넌 트

AAD 테 넌 트가 명시적으로 지정 되지 않은 경우 Kusto는 UPN (예:) (제공 된 경우)에서이를 확인 하려고 합니다 `johndoe@fabrikam.com` . 보안 주체가 UPN 형식이 아닌 테 넌 트 정보를 포함 하지 않는 경우에는 테 넌 트 ID 또는 이름을 주 설명자에 추가 하 여 명시적으로 언급 해야 합니다.

**AAD 보안 주체에 대 한 예제**

|AAD 테 넌 트 |Type |구문 |
|-----------|-----|-------|
|암시적 (UPN)  |사용자  |`aaduser=`*UserEmailAddress*
|Explicit (ID)   |사용자  |`aaduser=`*UserEmailAddress* `;` *TenantId* 또는 `aaduser=` *ObjectID* `;` *tenantid*
|Explicit (이름) |사용자  |`aaduser=`*UserEmailAddress* `;` *Tenantname* 또는 `aaduser=` *ObjectID* `;` *tenantname*
|암시적 (UPN)  |그룹 |`aadgroup=`*GroupEmailAddress*
|Explicit (ID)   |그룹 |`aadgroup=`*GroupObjectId* `;` *TenantId* 또는 `aadgroup=` *groupdisplayname* `;` *TenantId*
|Explicit (이름) |그룹 |`aadgroup=`*GroupObjectId* `;` *Tenantname* 또는 `aadgroup=` *groupdisplayname* `;` *tenantname*
|명시적 (UPN)  |앱   |`aadapp`=*Applicationdisplayname* `;` *TenantId*
|Explicit (이름) |앱   |`aadapp=`*ApplicationId* `;` *Tenantname*

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

**MSA 보안 주체에 대 한 예제**

|IdP  |Type  |구문 |
|-----|------|-------|
|Live.com |사용자  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

