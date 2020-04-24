---
title: Azure 활성 디렉터리(AAD) 인증 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 AAD(Azure Active Directory) 인증에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: 637252b91af53198b3ee494309857b2d4b6e6828
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522936"
---
# <a name="azure-active-directory-aad-authentication"></a>Azure 활성 디렉터리(AAD) 인증

AAD(Azure Active Directory) 는 보안 주체를 인증하거나 Microsoft의 Active Directory와 같은 다른 ID 공급자와 페더레이션할 수 있는 Azure의 기본 설정 다중 테넌트 클라우드 디렉터리 서비스입니다.

AAD는 다양한 종류의 응용 프로그램 (웹 응용 프로그램, Windows 데스크톱 응용 프로그램, 범용 응용 프로그램, 모바일 응용 프로그램 등)을 균일하게 인증하고 Kusto 서비스를 사용할 수 있습니다.

AAD는 여러 인증 시나리오를 지원합니다.
인증 중에 사용자가 있는 경우 AAD 사용자 인증을 통해 AAD에 사용자를 인증해야 합니다.
경우에 따라 사용자가 대화형으로 존재하지 않는 경우에도 Kusto를 사용하는 서비스를 원합니다. 이러한 경우 AAD 응용 프로그램 인증에 설명된 대로 응용 프로그램 비밀을 사용 하 여 응용 프로그램을 인증 해야 합니다.

.NET 라이브러리를 통해 Kusto에서 일반적으로 다음과 같은 인증 방법을 지원합니다.

* 대화형 사용자 인증 - 이 모드에는 필요한 경우 로그온 UI가 팝업처럼 상호 작용이 필요합니다.
* Kusto에 대해 이전에 발급된 기존 AAD 토큰이 있는 사용자 인증
* AppID 및 공유 비밀을 사용 하 고 응용 프로그램 인증
* 로컬로 설치된 X.509v2 인증서 또는 인라인으로 제공되는 인증서를 사용 하 고 응용 프로그램 인증
* Kusto에 대해 이전에 발급된 기존 AAD 토큰이 있는 응용 프로그램 인증
* 다른 리소스에 대해 발급된 AAD 토큰을 사용하여 사용자 또는 응용 프로그램 인증(해당 리소스와 Kusto 간에 신뢰가 있는 경우)

지침 및 예제는 [Kusto 연결 문자열](../../api/connection-strings/kusto.md) 참조를 참조하십시오.

## <a name="user-authentication"></a>사용자 인증

사용자 인증은 사용자가 자격 증명을 AAD(또는 ADFS와 같은 AAD와 페더레이션하는 일부 ID 공급자)에 제공할 때 수행되고, Kusto 서비스에 제공할 수 있는 보안 토큰을 다시 가져옵니다. Kusto 서비스는 보안 토큰을 얻는 방법에는 관심이 없지만, 토큰이 유효한지 여부와 AAD(또는 페더레이션된 IdP)에서 제공하는 정보에 관심이 있습니다.

클라이언트 쪽에서 Kusto는 AAD 클라이언트 라이브러리 ADAL 또는 이와 비슷한 코드에서 사용자에게 자격 증명을 입력하도록 요청하는 대화형 인증을 모두 지원합니다. 또한 Kusto를 사용하는 애플리케이션에서 유효한 사용자 토큰을 가져와서 표시하는 토큰 기반 인증도 지원합니다. 마지막으로, 이 기능은 해당 리소스와 Kusto 사이에 트러스트 관계가 있는 경우 Kusto를 사용하는 애플리케이션에서 Kusto가 아닌 다른 서비스에 대해 유효한 사용자 토큰을 가져오는 시나리오를 지원합니다.

Kusto 클라이언트 라이브러리를 사용하고 AAD를 Kusto에 사용하여 인증하는 방법에 대한 자세한 내용은 [Kusto 연결 문자열](../../api/connection-strings/kusto.md)을 참조하세요.

## <a name="application-authentication"></a>애플리케이션 인증

요청이 특정 사용자와 연결되지 않거나 자격 증명을 입력할 수 있는 사용자가 없는 경우 AAD 애플리케이션 인증 흐름을 사용할 수 있습니다. 이 흐름에서 애플리케이션은 몇 가지 비밀 정보를 제공하여 AAD(또는 페더레이션된 IdP)에 인증합니다. 다양한 Kusto 클라이언트에서 지원하는 시나리오는 다음과 같습니다.

* 로컬로 설치된 X.509v2 인증서를 사용하는 애플리케이션 인증입니다.
* 클라이언트 라이브러리에 바이트 스트림으로 제공된 X.509v2 인증서를 사용하는 애플리케이션 인증입니다.
* AAD 애플리케이션 ID 및 AAD 애플리케이션 키를 사용하는 애플리케이션 인증입니다(애플리케이션의 사용자 이름/암호 인증과 동일).
* 이전에 가져온 유효한 AAD 토큰(Kusto에 발급됨)을 사용하는 애플리케이션 인증입니다.
* 해당 리소스와 Kusto 사이에 트러스트 관계가 있는 경우 다른 리소스에 발급되었고 이전에 가져온 유효한 AAD 토큰을 사용하는 애플리케이션 인증입니다.

## <a name="aad-server-application-permissions"></a>AAD 서버 응용 프로그램 사용 권한

일반적으로 AAD 서버 응용 프로그램은 여러 권한(예: 읽기 전용 권한 및 읽기 기록기 권한)을 정의할 수 있으며 AAD 클라이언트 응용 프로그램은 권한 부여 토큰을 요청할 때 필요한 권한을 결정할 수 있습니다. 토큰 획득의 일환으로 사용자는 AAD 클라이언트 응용 프로그램이 이러한 권한을 가질 수 있는 권한을 부여하여 사용자를 대신하여 행동할 수 있도록 권한을 부여하라는 요청을 받게 됩니다. 사용자가 승인하면 이러한 사용 권한은 AAD 클라이언트 응용 프로그램에 발급된 토큰의 범위 클레임에 나열됩니다.



AAD 클라이언트 응용 프로그램은 사용자로부터 "Access Kusto" 권한을 요청하도록 구성됩니다(AAD는 "리소스 소유자"라고 합니다).

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>AAD 클라이언트 애플리케이션으로서의 Kusto 클라이언트 SDK

Kusto 클라이언트 라이브러리는 ADAL(AAD 클라이언트 라이브러리)을 호출하여 Kusto와 통신하기 위한 토큰을 획득할 때 다음 정보를 제공합니다.

1. 발신자로부터 받은 AAD 테넌트
2. AAD 클라이언트 애플리케이션 ID
3. AAD 클라이언트 리소스 ID
4. AAD ReplyUrl(AAD 서비스가 인증이 성공적으로 완료된 후 리디렉션되는 URL입니다. 그런 다음 ADAL은 이 리디렉션을 캡처하고 권한 부여 코드를 추출합니다.
5. 클러스터 URI(')'https://Cluster-and-region.kusto.windows.net

ADAL이 Kusto 클라이언트 라이브러리에 반환한 토큰에는 Kusto AAD 서버 응용 프로그램이 대상으로, "Kusto 액세스" 권한이 범위로 있습니다.

## <a name="authenticating-with-aad-programmatically"></a>AAD로 프로그래밍 방식으로 인증

다음 기사에서는 AAD를 사용하여 Kusto를 프로그래밍 방식으로 인증하는 방법을 설명합니다.

* [AAD 응용 프로그램을 프로비전하는 방법](./how-to-provision-aad-app.md)
* [AAD 인증을 수행하는 방법](./how-to-authenticate-with-aad.md)

