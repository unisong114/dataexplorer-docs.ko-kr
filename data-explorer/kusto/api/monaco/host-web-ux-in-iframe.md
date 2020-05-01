---
title: 웹 UI를 **iframe** 에 포함-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 **iframe** 에 웹 UI를 포함 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 7b7bb181f640eb259b32f3a5814290a218006418
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617955"
---
# <a name="embed-web-ui-in-an-iframe"></a>웹 UI를 iframe에 포함

Azure 데이터 탐색기 웹 UI는 iframe에 포함 되 고 타사 웹 사이트에서 호스팅될 수 있습니다.

![대체 텍스트](../images/web-ux.jpg "Azure 데이터 탐색기 웹 UI")

사용자는 웹 사이트에 Azure 데이터 탐색기 웹 UX를 포함 하 여 다음과 같은 작업을 수행할 수 있습니다.

- 쿼리 편집 (색 지정 및 intellisense와 같은 모든 언어 기능 포함)
- 시각적으로 테이블 스키마 탐색
- AAD에 인증
- 쿼리 실행
- 쿼리 실행 결과 표시
- 여러 탭 만들기
- 로컬에 쿼리 저장
- 전자 메일로 쿼리 공유

모든 기능은 내게 필요한 옵션에 대해 테스트 되 고 어두운 화면과 밝은 화면 테마를 지원 합니다.

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>모나코를 사용 하 여 웹 UI를 포함 하 시겠습니까?

모나코-Kusto는 완료, 색 지정, 리팩터링, 이름 바꾸기, 정의로 이동 등의 편집 환경을 제공 합니다. 이 기능을 사용 하려면 인증, 쿼리 실행, 결과 표시 및 스키마 탐색을 위한 솔루션을 빌드해야 하지만 사용자의 요구에 맞는 사용자 환경을 구현 하는 것은 완벽 한 유연성을 제공 합니다.

Azure 데이터 탐색기 웹 UI를 포함 하면 약간의 노력으로 광범위 한 기능을 제공 하지만 사용자 환경에 대 한 제한 된 유연성을 제공 합니다. 시스템의 모양과 동작을 제한 하 여 제어할 수 있는 고정 쿼리 매개 변수 집합이 있습니다.

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>웹 UI를 iframe에 포함 하는 방법

### <a name="host-the-website-in-an-iframe"></a>Iframe에서 웹 사이트 호스팅

웹 사이트에 다음 코드를 추가 합니다.

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

쿼리 `ibizaPortal` 매개 변수는 인증 토큰을 가져오기 위해 리디렉션하지 *않도록* Azure 데이터 탐색기 웹 UI에 지시 합니다. 호스팅 웹 사이트에서 포함 된 iframe에 인증 토큰을 제공 하는 작업을 담당 하므로이 작업은 필수입니다.

을 `<cluster>` 연결 창에 로드 하려는 클러스터의 호스트 이름으로 바꿉니다 (예: `help.kusto.windows.net`). 기본적으로 iframe 포함 모드는 호스팅 웹 사이트가 필요한 클러스터를 인식 하는 것으로 가정 하므로 UI에서 클러스터를 추가 하는 방법을 제공 하지 않습니다.

### <a name="handle-authentication"></a>인증 처리

1. ' Iframe mode ' (`ibizaPortal=true`)로 설정 되 면 Azure 데이터 탐색기 웹 UI는 인증을 위해 리디렉션을 시도 하지 않습니다. 웹 UI는 브라우저에서 사용 하는 메시지 게시 메커니즘을 사용 하 여 토큰을 요청 하 고 수신 합니다. 페이지를 로드 하는 동안 다음 메시지가 부모 창에 게시 됩니다.

   ```javascript
   window.parent.postMessage(
     {
       signature: "queryExplorer",
       type: "getToken"
     },
     "*"
   );
   window.addEventListener(
     "message",
     event => this.handleIncomingMessage(event),
     false
   );
   ```

1. 그런 다음 구조가 다음과 같은 메시지를 수신 대기 합니다.

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. 제공 된 토큰은 [[AAD 인증 끝점]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)에서 가져온 [JWT 토큰](https://tools.ietf.org/html/rfc7519) 이어야 합니다.

> [!IMPORTANT]
> 호스팅 윈도는 만료 전에 토큰을 새로 고쳐야 하며, 동일한 메커니즘을 사용 하 여 업데이트 된 토큰을 응용 프로그램에 제공 해야 합니다. 그렇지 않으면 토큰이 만료 되 면 서비스 호출이 실패 합니다.

### <a name="feature-flags"></a>기능 플래그

호스팅 응용 프로그램은 사용자 환경의 특정 측면을 제어할 수 있습니다. 예를 들어 연결 창을 숨기 거 나 다른 클러스터에 대 한 연결을 사용 하지 않도록 설정할 수 있습니다.
이 시나리오의 경우 웹 탐색기는 기능 플래그를 지원 합니다.

기능 플래그는 url에서 쿼리 매개 변수로 사용할 수 있습니다. 예를 들어 호스팅 응용 프로그램에서 사용 해야 하는 다른 클러스터 추가를 사용 하지 않도록 설정 하려는 경우https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 설정                 | Description                                                                                                                                                                                                                                                                                       | 기본값 |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| ShowShareMenu           | 공유 메뉴 항목 표시                                                                                                                                                                                                                                                                          | true          |
| ShowConnectionButtons   | 새 클러스터를 추가 하는 **연결 추가** 단추 표시                                                                                                                                                                                                                                               | true          |
| ShowOpenNewWindowButton | 웹 UI **에서 열기** 단추를 표시 합니다 .이 단추를 사용 하면 범위 내에 있는 https://dataexplorer.azure.com 올바른 클러스터와 데이터베이스를 가리키는 새 브라우저 창이 열립니다.                                                                                                                                   | false         |
| ShowFileMenu            | 파일 메뉴 표시 (**다운로드**, **탭**, **콘텐츠**등)                                                                                                                                                                                                                                                     | true          |
| ShowToS                 | 설정 대화 상자에서 **Azure 데이터 탐색기에 대 한 서비스 약관에 대 한 링크를** 표시 합니다.                                                                                                                                                                                                                | true          |
| ShowPersona 사용자             | 설정 메뉴의 오른쪽 위 모서리에 사용자 이름을 표시 합니다.                                                                                                                                                                                                                                         | true          |
| I프레임 인증              | True 이면 웹 탐색기에서 iframe이 인증을 처리 하 고 메시지를 통해 토큰을 제공 하는 것으로 간주 합니다. 이 프로세스는 iframe 시나리오에서 항상 true입니다.                                                                                                                                              | false         |
| PersistAfterEachRun     | 일반적으로 웹 탐색기는 언로드 이벤트에서 유지 됩니다 (iframe에서 호스팅할 때 항상 발생 하지는 않음). 그러면이 플래그는 각 쿼리를 실행 한 후에 **로컬 상태 유지** 를 트리거합니다. 따라서 발생 하는 데이터 손실은 실행 된 적이 없는 텍스트에만 영향을 주므로 영향을 제한 합니다. | false         |
| ShowSmoothIngestion     | True 이면 데이터베이스를 마우스 오른쪽 단추로 클릭할 때 1 클릭 수집 환경을 표시 합니다.                                                                                                                                                                                                                  | true          |
| RefreshConnection       | True 이면 항상 페이지를 로드할 때 스키마를 새로 고치고 로컬 저장소에 종속 되지 않습니다.                                                                                                                                                                                                      | false         |
| ShowPageHeader          | True 이면 Azure 데이터 탐색기 제목과 설정을 포함 하는 페이지 머리글을 표시 합니다.                                                                                                                                                                                                 | true          |
| HideConnectionPane      | True 이면 왼쪽 연결 창이 표시 되지 않습니다.                                                                                                                                                                                                                                               | false         |
| SkipMonacoFocusOnInit   | Iframe에서 호스팅할 때 포커스 문제를 수정 합니다.                                                                                                                                                                                                                                                          | false         |

### <a name="feature-flag-presets"></a>기능 플래그 사전 설정

미리 설정은 한 번에 다양 한 기능 플래그를 설정 합니다.
현재는 미리 설정이 하나만 있습니다.

`IbizaPortal`-기본값에서 다음 플래그를 변경 합니다.

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> 미리 설정을 사용 하는 경우이를 기반으로 기능 플래그를 더 추가할 수 없습니다. 이 유연성이 필요한 경우 개별 기능 플래그를 사용 해야 합니다.
