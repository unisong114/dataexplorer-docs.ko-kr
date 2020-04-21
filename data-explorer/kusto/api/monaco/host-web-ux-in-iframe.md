---
title: iframe에 웹 UI 포함 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 iframe에 웹 UI 포함에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9c5469a577c3e09cd433ec250f9215e5f450d5f5
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524466"
---
# <a name="embed-web-ui-in-an-iframe"></a>iframe에 웹 UI 포함

Azure 데이터 탐색기 웹 UI는 iframe에 포함되고 타사 웹 사이트에서 호스팅될 수 있습니다.

![대체 텍스트](../images/web-ux.jpg "Azure 데이터 탐색기 웹 UI")

웹 사이트에 Azure 데이터 탐색기 웹 UX를 포함하면 사용자가 다음을 수행할 수 있습니다.

- 쿼리 편집(색상 지정 및 인텔리센스와 같은 모든 언어 기능 포함)
- 테이블 스키마를 시각적으로 탐색
- AAD로 인증
- 쿼리 실행
- 쿼리 실행 결과 표시
- 여러 탭 만들기
- 쿼리를 로컬로 저장
- 이메일로 쿼리 공유

모든 기능은 접근성테스트를 거쳤으며 어둡고 밝은 테마를 지원합니다.

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>모나코 쿠스토를 사용하거나 웹 UI를 포함?

Monaco-Kusto는 완료, 색상 지정, 리팩터링, 이름 바꾸기 및 정의로 이동과 같은 편집 환경을 제공하므로 인증, 쿼리 실행, 결과 표시 및 스키마 탐색을 위한 솔루션을 빌드해야 합니다. 반면에 사용자의 요구에 맞는 사용자 환경을 유연하게 유연하게 사용할 수 있습니다.

Azure Data Explorer 웹 UI를 포함하면 약간의 노력으로 광범위한 기능을 제공하지만 사용자 환경과 관련된 유연성은 제한적입니다. 시스템의 모양과 동작을 제한적으로 제어할 수 있는 고정된 쿼리 매개 변수 집합이 있습니다.

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>iframe에 웹 UI를 포함 하는 방법

### <a name="host-the-website-in-iframe"></a>iframe에서 웹 사이트 호스트

웹사이트에 다음 코드를 추가합니다.

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

쿼리 `ibizaPortal` 매개 변수는 Azure Data Explorer 웹 UI가 인증 토큰을 얻기 위해 _리디렉션되지 않도록_ 지시합니다. 호스팅 웹 사이트가 임베디드 iframe에 인증 토큰을 제공하므로 이 필요합니다.

연결 `<cluster>` 창에 로드할 클러스터의 호스트 이름으로 바꿉니다(for). `example: help.kusto.windows.net` 기본적으로 iframe-embedded 모드는 호스팅 웹 사이트가 필요한 클러스터를 알고 있다는 가정하에 UI에서 클러스터를 추가하는 방법을 제공하지 않습니다.

### <a name="handle-authentication"></a>인증 처리

1. 'iframe 모드'()로`ibizaPortal=true`설정하면 Azure 데이터 탐색기 웹 UI가 인증을 위해 리디렉션하려고 시도하지 않습니다. 웹 UI는 브라우저가 토큰을 요청하고 받는 데 사용하는 메시지 게시 메커니즘을 사용합니다. 페이지를 로드하는 동안 다음 메시지가 부모 창에 게시됩니다.

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

1. 그런 다음 다음과 같은 구조의 메시지를 수신합니다.

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. 제공된 토큰은 [[AAD 인증 끝점]에서](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)얻은 [JWT 토큰이어야](https://tools.ietf.org/html/rfc7519) 합니다.

> [!IMPORTANT]
> 만료 되기 전에 토큰을 새로 고치고 동일한 메커니즘을 사용하여 업데이트된 토큰을 응용 프로그램에 제공하는 것은 호스팅 창의 책임입니다. 그렇지 않으면 토큰이 만료되면 서비스 호출은 항상 실패합니다.

### <a name="feature-flags"></a>기능 플래그

호스팅 응용 프로그램은 사용자 환경의 특정 측면을 구성할 수 있습니다. 예를 들어 연결 창을 숨기거나 다른 클러스터에 대한 연결을 비활성화합니다.
이 시나리오에서는 웹 탐색기가 기능 플래그를 지원합니다.

기능 플래그는 URL에서 쿼리 매개 변수로 사용할 수 있습니다. 예를 들어 호스팅 응용 프로그램이 다른 클러스터 추가를 사용하지 않으려면https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 설정                 | Description                                                                                                                                                                                                                                                                                       | 기본값 |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| 쇼쉐어메뉴           | 공유 메뉴 항목 표시                                                                                                                                                                                                                                                                          | true          |
| 연결 단추 표시   | 새 클러스터를 추가하기 위한 연결 추가 단추 표시                                                                                                                                                                                                                                               | true          |
| 쇼오픈뉴윈도우버튼 | "웹 UI 버튼에서 열기" 표시합니다. 이렇게 하면 범위에 올바른 클러스터 및 https://dataexplorer.azure.com 데이터베이스가 있는 것을 가리키는 새 브라우저 창이 열립니다.                                                                                                                                   | false         |
| 쇼파일메뉴            | 파일 메뉴 표시(다운로드 탭 콘텐츠 ETC)                                                                                                                                                                                                                                                     | true          |
| 쇼토스                 | 설정 대화 상자에서 Azure 데이터 탐색기의 서비스 약관에 대한 링크 표시                                                                                                                                                                                                                | true          |
| 쇼 페르소나             | 오른쪽 상단과 설정 메뉴에서 사용자 이름 표시                                                                                                                                                                                                                                        | true          |
| 아이프레임아우트              | true이면 웹 탐색기는 iframe이 인증을 처리하고 메시지를 통해 토큰을 제공할 것으로 예상합니다. 이것은 항상 iframe 시나리오에 대 한 사실 일 것 이다                                                                                                                                              | false         |
| 지속 후각 실행     | 일반적으로 웹 탐색기는 온언로드 이벤트에 유지됩니다. iframes에서 호스팅 할 때 어떤 경우에는 발사되지 않습니다. 따라서 이 플래그는 각 쿼리가 실행된 후 로컬 상태를 방해합니다. 따라서 발생하는 데이터 손실은 실행되지 않은 텍스트만 영향을 주므로 영향을 제한합니다. | false         |
| 쇼스무딩     | true이면 데이터베이스를 마우스 오른쪽 단추로 클릭할 때 1회 클릭 으로 사용 환경을 표시합니다.                                                                                                                                                                                                                  | true          |
| 새로 고침 연결       | true이면 페이지를 로드할 때 항상 스키마를 새로 고치고 로컬 저장소에 종속되지 않습니다.                                                                                                                                                                                                      | false         |
| 쇼페이지헤더          | true이면 페이지 헤더(Azure 데이터 탐색기 제목, 설정 및 기타 포함)가 표시됩니다.                                                                                                                                                                                                 | true          |
| 숨기기연결파인      | true이면 왼쪽 연결 창이 표시되지 않습니다.                                                                                                                                                                                                                                                | false         |
| 스킵모나코포커스니트   | iframe에서 호스팅할 때 포커스 문제 수정                                                                                                                                                                                                                                                          | false         |

### <a name="feature-flag-presets"></a>피처 플래그 사전 설정

사전 설정은 한 번에 기능 플래그의 무리를 설정합니다.
오늘 우리는 하나의 사전 설정이 있습니다.

`IbizaPortal`- 기본값에서 다음 플래그를 변경합니다.

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> 사전 설정을 사용하는 경우 위에 피쳐 플래그를 추가할 수 없습니다. 이러한 유연성이 필요한 경우 개별 피쳐 플래그를 사용해야 합니다.
