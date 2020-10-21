---
title: AAD (kusto Azure Active Directory) 인증-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 AAD (Azure Active Directory) 인증을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: a511f96df09fa9ce4089a0af77ebbe09269c9b44
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343439"
---
# <a name="azure-active-directory-aad-authentication"></a>AAD (Azure Active Directory) 인증

AAD(Azure Active Directory) 는 보안 주체를 인증하거나 Microsoft의 Active Directory와 같은 다른 ID 공급자와 페더레이션할 수 있는 Azure의 기본 설정 다중 테넌트 클라우드 디렉터리 서비스입니다.

AAD를 통해 다양 한 종류의 응용 프로그램 (웹 응용 프로그램, Windows 데스크톱 응용 프로그램, 유니버설 응용 프로그램, 모바일 응용 프로그램 등)을 균일 하 게 인증 하 고 Kusto 서비스를 사용할 수 있습니다.

AAD는 다양 한 인증 시나리오를 지원 합니다.
인증 하는 동안 사용자가 있는 경우 AAD 사용자 인증을 사용 하 여 AAD에 사용자를 인증 해야 합니다.
어떤 경우에는 대화형으로 사용자가 없는 경우에도 Kusto를 사용 하는 서비스를 원합니다. 이러한 경우 AAD 응용 프로그램 인증에 설명 된 대로 응용 프로그램 암호를 사용 하 여 응용 프로그램을 인증 해야 합니다.

다음 인증 방법은 .NET 라이브러리를 비롯 하 여 일반적으로 Kusto에서 지원 됩니다.

* 대화형 사용자 인증-이 모드를 사용 하려면 필요에 따라 로그온 UI가 팝업 됩니다.
* 이전에 Kusto에 대해 발급 한 기존 AAD 토큰을 사용 하 여 사용자 인증
* AppID 및 공유 암호를 사용 하는 응용 프로그램 인증
* 로컬로 설치 된 응용 프로그램 인증 또는 인라인으로 제공 된 인증서 또는 인증서
* 이전에 Kusto에 대해 발급 된 기존 AAD 토큰으로 응용 프로그램 인증
* 다른 리소스에 대해 발급 된 AAD 토큰을 사용 하는 사용자 또는 응용 프로그램 인증, 해당 리소스와 Kusto 간에 트러스트가 존재 합니다.

지침과 예제는 [Kusto 연결 문자열](../../api/connection-strings/kusto.md) 참조를 참조 하세요.

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

일반적으로 AAD 서버 응용 프로그램은 여러 사용 권한 (예: 읽기 전용 권한 및 읽기/쓰기 권한)을 정의할 수 있으며, AAD 클라이언트 응용 프로그램은 권한 부여 토큰을 요청할 때 필요한 권한을 결정할 수 있습니다. 토큰 획득의 일부로 사용자에 게 권한 부여를 사용 하 여 사용자를 대신 하 여 AAD 클라이언트 응용 프로그램에 권한을 부여 하 라는 메시지가 표시 됩니다. 사용자가 승인 하는 경우 이러한 사용 권한은 AAD 클라이언트 응용 프로그램에 발급 된 토큰의 범위 클레임에 나열 됩니다.



AAD 클라이언트 응용 프로그램은 사용자 (AAD에서 "리소스 소유자"를 호출 하는)에서 "Access Kusto" 권한을 요청 하도록 구성 됩니다.

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>AAD 클라이언트 애플리케이션으로서의 Kusto 클라이언트 SDK

Kusto 클라이언트 라이브러리는 ADAL(AAD 클라이언트 라이브러리)을 호출하여 Kusto와 통신하기 위한 토큰을 획득할 때 다음 정보를 제공합니다.

1. 호출자 로부터 받은 AAD 테 넌 트
2. AAD 클라이언트 애플리케이션 ID
3. AAD 클라이언트 리소스 ID
4. AAD ReplyUrl (AAD 서비스가 인증 후에 리디렉션되는 URL이 성공적으로 완료 됩니다. 그런 다음 ADAL은이 리디렉션을 캡처하고 인증 코드를 추출 합니다.
5. 클러스터 URI (' https://Cluster-and-region.kusto.windows.net ')입니다.

ADAL에서 Kusto 클라이언트 라이브러리로 반환 되는 토큰은 Kusto AAD 서버 응용 프로그램을 대상으로 하며 "Access Kusto" 권한을 범위로 사용 합니다.

## <a name="authenticating-with-aad-programmatically"></a>프로그래밍 방식으로 AAD 인증

다음 문서에서는 AAD를 사용 하 여 Kusto에 프로그래밍 방식으로 인증 하는 방법을 설명 합니다.

* [AAD 응용 프로그램을 프로 비전 하는 방법](../../../provision-azure-ad-app.md)
* [AAD 인증을 수행 하는 방법](./how-to-authenticate-with-aad.md)