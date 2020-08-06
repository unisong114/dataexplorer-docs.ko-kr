---
title: Kusto.Explorer 사용
description: Kusto 탐색기를 사용 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 8688c7d6f2a5d68e0e1ae4436e099b011fd5faa0
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803236"
---
# <a name="using-kustoexplorer"></a>Kusto.Explorer 사용

Kusto. 탐색기는 사용 하기 쉬운 사용자 인터페이스에서 Kusto 쿼리 언어를 사용 하 여 데이터를 탐색할 수 있도록 하는 데스크톱 응용 프로그램입니다. 이 문서에서는 검색 및 쿼리 모드를 사용 하 고, 쿼리를 공유 하 고, 클러스터, 데이터베이스 및 테이블을 관리 하는 방법을 보여 줍니다.

## <a name="search-mode"></a>검색 + + 모드

검색 + + 모드에서는 하나 이상의 테이블에서 검색 구문을 사용 하 여 용어를 검색할 수 있습니다.

1. 홈 탭의 쿼리 드롭다운에서 **검색 + +** 를 선택 합니다.
1. **여러 테이블**을 선택 합니다.
1. **테이블 선택**에서 검색할 테이블을 정의 합니다.
1. 편집 상자에 검색 문구를 입력 하 고 **이동**을 선택 합니다.
1. 테이블/시간 슬롯 표의 열 맵은 표시 되는 용어와 표시 되는 위치를 보여 줍니다.

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="검색 + + Kusto Explorer":::

1. 표에서 셀을 선택 하 고 **자세히 보기** 를 선택 하 여 결과 창에 관련 항목을 표시 합니다.

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="Kusto 탐색기 검색 + + 결과":::

## <a name="query-mode"></a>쿼리 모드

Kusto. 탐색기에는 임시 쿼리를 작성, 편집 및 실행할 수 있는 강력한 스크립트 모드가 포함 되어 있습니다. 스크립트 모드는 구문 강조 표시 및 IntelliSense와 함께 제공 되므로 Kusto 쿼리 언어에 대 한 지식을 신속 하 게 설정할 수 있습니다.

이 섹션에서는 Kusto. 탐색기에서 기본 쿼리를 실행 하는 방법과 쿼리에 매개 변수를 추가 하는 방법에 대해 설명 합니다.

## <a name="basic-queries"></a>기본 쿼리

테이블 로그가 있으면 탐색을 시작할 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

이 줄에 커서가 있는 경우 회색으로 표시 됩니다. **F5** 키를 눌러 쿼리를 실행 합니다. 

다음은 몇 가지 추가 예제 쿼리입니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="Kusto 탐색기 기본 쿼리":::

[Kusto 쿼리 언어](https://docs.microsoft.com/azure/kusto/query/)에 대해 자세히 알아보세요.

> [!NOTE]
> 쿼리 식의 빈 줄은 실행되는 쿼리 부분에 영향을 줄 수 있습니다.
>
> 텍스트를 선택하지 않은 경우 쿼리나 명령이 빈 줄로 구분되어 있다고 가정합니다.
> 텍스트를 선택하면 선택한 텍스트가 실행됩니다.

## <a name="client-side-query-parameterization"></a>클라이언트 쪽 쿼리 매개 변수화

> [!NOTE]
> Kusto에는 두 가지 유형의 query parametrization 기술이 있습니다.
> * [언어 통합 쿼리 parametrization](../query/queryparametersstatement.md) 는 쿼리 엔진의 일부로 구현 되며 서비스를 프로그래밍 방식으로 쿼리 하는 응용 프로그램에서 사용 됩니다. 이 방법은이 문서에 설명 되어 있지 않습니다.
>
> * 아래에 설명 된 클라이언트 쪽 쿼리 parametrization는 Kusto 탐색기 응용 프로그램의 기능입니다. 서비스에서 실행 되도록 보내기 전에 쿼리에 대해 문자열 바꾸기 작업을 사용 하는 것과 같습니다. 아래에 설명 된 구문은 쿼리 언어 자체에 포함 되지 않으며, Kusto 탐색기를 사용 하 여 쿼리를 서비스로 보낼 때 사용할 수 없습니다.

여러 쿼리 또는 여러 탭에서 동일한 값을 사용 하는 경우 사용 되는 모든 위치의 값을 변경 하는 것이 매우 불편할 수 있습니다. 그 이유는 Kusto 탐색기는 쿼리 매개 변수를 지원 하기 때문입니다. 쿼리 매개 변수는 쉽게 다시 사용할 수 있도록 탭 간에 공유 됩니다. 매개 변수는 대괄호로 표시 됩니다 {} . 예를 들면 다음과 같습니다. `{parameter1}`

스크립트 편집기는 쿼리 매개 변수를 강조 표시 합니다.

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="매개 변수가 있는 쿼리 1":::

기존 쿼리 매개 변수를 쉽게 정의 하 고 편집할 수 있습니다.


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="매개 변수가 있는 쿼리 2 편집":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="매개 변수가 있는 쿼리 편집 3":::

스크립트 편집기에는 이미 정의 된 쿼리 매개 변수에 대 한 IntelliSense도 있습니다.

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="Paramaterized 쿼리 IntelliSense":::

매개 변수 **집합** 콤보 상자에 나열 된 여러 매개 변수 집합을 사용할 수 있습니다.
매개 변수 집합 목록을 조작 하려면 **새로 추가** 또는 **현재 삭제** 를 선택 합니다.

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="매개 변수 집합 목록":::

## <a name="share-queries-and-results"></a>쿼리 및 결과 공유

Kusto 탐색기에서 쿼리와 결과를 전자 메일로 공유할 수 있습니다. 브라우저에서 쿼리를 열고 실행 하는 딥 링크를 만들 수도 있습니다.

### <a name="share-queries-and-results-by-email"></a>전자 메일로 쿼리 및 결과 공유

Kusto 탐색기는 쿼리 및 쿼리 결과를 전자 메일로 공유 하는 편리한 방법을 제공 합니다.

1. Kusto 탐색기에서 [쿼리를 실행](#basic-queries) 합니다.
1. 홈 탭의 공유 섹션에서 **클립보드로 내보내기** 를 선택 하거나 Ctrl + Shift + C를 누릅니다.

    :::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="클립보드로 내보내기":::

    Kusto 탐색기는 다음 내용을 클립보드에 붙여 넣습니다.
     * 쿼리
     * 쿼리 결과 (테이블 또는 차트)
     * Kusto 클러스터 및 데이터베이스에 대 한 연결 세부 정보
     * 자동으로 쿼리를 다시 실행 하는 링크

1. 클립보드의 내용을 새 전자 메일 메시지에 붙여넣습니다.

    :::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="전자 메일로 결과 공유":::

### <a name="deep-linking-queries"></a>심층 연결 쿼리

브라우저에서 열 때이 URI를 만들 수 있습니다. 탐색기를 로컬로 열고 지정 된 Kusto 데이터베이스에서 특정 쿼리를 실행 합니다.

> [!NOTE] 
> 보안상의 이유로, 제어 명령에 대해 딥 링크를 사용할 수 없습니다.

#### <a name="creating-a-deep-link"></a>딥 링크 만들기

딥 링크를 만드는 가장 쉬운 방법은 Kusto. 탐색기에서 쿼리를 작성 한 다음 `Export to Clipboard` 를 사용 하 여 쿼리 (딥 링크 및 결과 포함)를 클립보드에 복사 하는 것입니다. 그런 다음 전자 메일을 통해 공유할 수 있습니다.
        
전자 메일에 복사 된 경우 딥 링크는 작은 글꼴로 표시 됩니다. 예를 들어:

https://help.kusto.windows.net:443/Samples[[쿼리를 실행 하려면 클릭](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

첫 번째 링크는 Kusto. 탐색기를 열고 클러스터 및 데이터베이스 컨텍스트를 적절 하 게 설정 합니다.
두 번째 링크 ( `Click to run query` )는 딥 링크입니다. 링크를 전자 메일 메시지로 이동 하 고 CTRL + K를 누르면 실제 URL을 볼 수 있습니다.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

#### <a name="deep-links-and-parametrized-queries"></a>딥 링크 및 매개 변수가 있는 쿼리

딥 링크와 함께 매개 변수가 있는 쿼리를 사용할 수 있습니다.

1. 매개 변수가 있는 쿼리 (예:)로 형성 될 쿼리를 만듭니다. `KustoLogs | where Timestamp > ago({Period}) | count` 
1. URI의 모든 쿼리 매개 변수 (예:)에 대 한 매개 변수를 제공 합니다. 
    
    `https://<your_cluster>.kusto.windows.net/MyDatabase?
web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

    &lt;Your_cluster &gt; 를 Azure 데이터 탐색기 클러스터 이름으로 바꿉니다.

#### <a name="limitations"></a>제한 사항

브라우저 제한, HTTP 프록시 및 Microsoft Outlook과 같은 링크의 유효성을 검사 하는 도구로 인해 쿼리가 ~ 2000 자로 제한 됩니다. 클러스터와 데이터베이스 이름 길이에 따라 달라 지므로 제한이 대략적으로 발생 합니다. 자세한 내용은 [https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427)를 참조하세요. 

문자 제한에 도달할 가능성을 줄이려면 [짧은 링크 가져오기](#getting-shorter-links)를 참조 하세요.

URI의 형식은 다음과 같습니다.`https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

예를 들어:[https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
이 URI는 Kusto 탐색기를 열고, `Help` kusto 클러스터에 연결 하 고, 데이터베이스에서 지정 된 쿼리를 실행 합니다. `Samples` Kusto. 탐색기 인스턴스가 이미 실행 중인 경우 실행 중인 인스턴스는 새 탭을 열고 쿼리를 실행 합니다.

### <a name="getting-shorter-links"></a>짧은 링크 가져오기

쿼리가 길어질 수 있습니다. 쿼리가 최대 길이를 초과 하는 가능성을 줄이려면 `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` Kusto 클라이언트 라이브러리에서 사용할 수 있는 메서드를 사용 합니다. 이 메서드는 더 간결한 버전의 쿼리를 생성 합니다. 더 짧은 형식은 Kusto 탐색기에서도 인식 됩니다.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

다음 변환을 적용 하 여 쿼리를 더 간결 하 게 만들 수 있습니다.

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto 탐색기 명령줄 인수

명령줄 인수는 시작 시 추가 기능을 수행 하도록 도구를 구성 하는 데 사용 됩니다. 예를 들어 스크립트를 로드 하 고 클러스터에 연결 합니다. 따라서 명령줄 인수는 모든 Kusto 탐색기 기능을 대체 하지 않습니다.

명령줄 인수는 [딥 링크를 쿼리](#creating-a-deep-link)하는 것과 비슷한 방법으로 응용 프로그램을 여는 데 사용 되는 URL의 일부로 전달 됩니다.

### <a name="command-line-argument-syntax"></a>명령줄 인수 구문

Kusto 탐색기는 다음 구문에서 몇 가지 명령줄 인수를 지원 합니다 (순서는 중요).

[*LocalScriptFile*] [*QueryString*]

* *LocalScriptFile* 은 로컬 컴퓨터에 있는 스크립트 파일의 이름입니다. 여기에는 확장명이 있어야 합니다 `.kql` . 이러한 파일이 있는 경우 파일을 시작 하면이 파일이 자동으로 로드 됩니다.
* *QueryString* 은 HTTP 쿼리 문자열 형식을 사용 하는 문자열입니다. 이 메서드는 아래 표에 설명 된 대로 추가 속성을 제공 합니다.

예를 들어 `c:\temp\script.kql` 클러스터, 데이터베이스와 통신 하도록 구성 된 스크립트 파일과 함께 Kusto 탐색기를 시작 하려면 `help` `Samples` 다음 명령을 사용 합니다.

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|인수  |설명                                                               |
|----------|--------------------------------------------------------------------------|
|**실행할 쿼리**                                                                 |
|`query`   |실행할 쿼리 (base64 인코딩)입니다. 비어 있는 경우를 사용 `querysrc` 합니다.          |
|`querysrc`|실행할 쿼리를 보유 하는 파일 또는 blob의 URL ( `query` 가 비어 있는 경우)입니다.|
|**Kusto 클러스터에 연결**                                                  |
|`uri`     |연결할 Kusto 클러스터의 연결 문자열입니다.                 |
|`name`    |Kusto 클러스터에 대 한 연결의 표시 이름입니다.                  |
|**연결 그룹**                                                                 |
|`path`    |다운로드할 연결 그룹 파일의 URL (URL 인코딩)입니다.             |
|`group`   |연결 그룹의 이름입니다.                                         |
|`filename`|연결 그룹을 보유 하는 로컬 파일입니다.                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>클러스터, 데이터베이스, 테이블 또는 함수 인증 된 보안 주체 관리

> [!NOTE]
> [관리자](../management/access-control/role-based-authorization.md) 만 인증 된 보안 주체를 자체 범위에 추가 하거나 삭제할 수 있습니다.

[연결 패널](kusto-explorer.md#connections-tab)에서 대상 엔터티를 마우스 오른쪽 단추로 클릭 하 고 **클러스터 권한 부여 된 보안 주체 관리**를 선택 합니다. (관리 메뉴에서이 옵션을 선택할 수도 있습니다.)

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="권한 있는 보안 주체 관리":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="권한 있는 사용자 관리 창":::

* 새 인증 된 보안 주체를 추가 하려면 **보안 주체 추가**를 선택 하 고, 보안 주체 세부 정보를 제공 하 고, 작업을 확인 합니다.
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="인증 된 보안 주체 추가":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="인증 된 보안 주체 추가 확인":::

* 권한 있는 기존 보안 주체를 삭제 하려면 **보안 주체 삭제** 를 선택 하 고 작업을 확인 합니다.

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="권한 있는 사용자 삭제 확인":::


## <a name="next-steps"></a>다음 단계

* [Kusto 탐색기 바로 가기 키](kusto-explorer-shortcuts.md)
* [Kusto.Explorer 옵션](kusto-explorer-options.md)
* [Kusto.Explorer 문제 해결](kusto-explorer-troubleshooting.md)

Kusto 탐색기 도구 및 유틸리티에 대해 자세히 알아보세요.
* [Kusto 탐색기 코드 분석기](kusto-explorer-code-analyzer.md)
* [Kusto 탐색기 코드 탐색](kusto-explorer-codenav.md)
* [Kusto 탐색기 코드 리팩터링](kusto-explorer-refactor.md)
* [Kusto Query Language(KQL)](https://docs.microsoft.com/azure/kusto/query/)
