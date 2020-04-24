---
title: HTTPS를 통해 인증 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 HTTPS에 대한 인증에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 4b6fbf5bb34dc3ff52938c7042778a7e49fd5faf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503046"
---
# <a name="authentication-over-https"></a>HTTPS를 통해 인증

HTTPS를 사용하는 경우 서비스는 `Authorization` 인증 수행을 위한 표준 HTTP 헤더를 지원합니다.

지원되는 HTTP 인증 방법은 다음과 같습니다.

* **Azure Active Directory** `bearer` 는 메서드를 통해

Azure Active Directory를 사용하여 인증할 때 `Authorization` 헤더에는 다음 형식이 있습니다.

```txt
Authorization: bearer TOKEN
```

호출자가 다음 속성을 사용 하 여 Azure Active Directory 서비스와 통신 하 여 획득 하는 액세스 토큰은 어디입니다. `TOKEN`

* 리소스는 서비스 URI(예: `https://help.kusto.windows.net`)입니다.
* Azure Active 디렉터리 서비스 `https://login.microsoftonline.com/TENANT/`끝점은 .

Azure `TENANT` Active Directory 테넌트 ID 또는 이름은 어디에 있습니까? 예를 들어 Microsoft 테넌트에서 `https://login.microsoftonline.com/microsoft.com/`만든 서비스는 을 사용할 수 있습니다. 또는 사용자 인증에 대해서만 대신 요청을 할 `https://login.microsoftonline.com/common/` 수 있습니다.

> [!NOTE]
> Azure Active Directory 서비스 끝점이 국가 클라우드에서 실행될 때 변경됩니다.
> 사용할 끝점을 변경하려면 환경 변수를 `AadAuthorityUri` 필요한 URI로 설정합니다.

자세한 내용은 [인증 개요](../../management/access-control/index.md) 및 Azure [Active Directory 인증에 대한 가이드를](../../management/access-control/how-to-authenticate-with-aad.md)참조하세요.