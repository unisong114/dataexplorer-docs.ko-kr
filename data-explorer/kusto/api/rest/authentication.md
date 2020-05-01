---
title: HTTPS를 통한 인증-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 HTTPS를 통한 인증을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: e0910089d87d6bce6124cb7e4560c2fa7b92b847
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617989"
---
# <a name="authentication-over-https"></a>HTTPS를 통한 인증

HTTPS를 사용 하는 경우 서비스는 인증을 `Authorization` 수행 하기 위한 표준 HTTP 헤더를 지원 합니다.

지원 되는 HTTP 인증 방법은 다음과 같습니다.

* 메서드를 통해 Azure Active Directory 합니다. **Azure Active Directory** `bearer`

Azure AD를 사용 하 여 인증 `Authorization` 하는 경우 헤더의 형식은 다음과 같습니다.

```txt
Authorization: bearer TOKEN
```

여기서 `TOKEN` 는 Azure AD 서비스와 통신 하 여 호출자가 획득 하는 액세스 토큰입니다. 토큰에는 다음과 같은 속성이 있습니다.

* 리소스는 서비스 URI (예: `https://help.kusto.windows.net`)입니다.
* Azure AD 서비스 끝점은`https://login.microsoftonline.com/TENANT/`

여기서 `TENANT` 는 Azure AD 테 넌 트 ID 또는 이름입니다. 예를 들어 Microsoft 테 넌 트에서 생성 된 서비스는을 사용할 `https://login.microsoftonline.com/microsoft.com/`수 있습니다. 또는 사용자 인증의 경우에 `https://login.microsoftonline.com/common/`만 요청을 수행할 수 있습니다.

> [!NOTE]
> Azure AD 서비스 끝점은 국가별 클라우드에서 실행 될 때 변경 됩니다.
> 끝점을 변경 하려면 환경 변수 `AadAuthorityUri` 를 필요한 URI로 설정 합니다.

자세한 내용은 [인증 개요](../../management/access-control/index.md) 및 [Azure AD 인증에](../../management/access-control/how-to-authenticate-with-aad.md)대 한 가이드를 참조 하세요.
