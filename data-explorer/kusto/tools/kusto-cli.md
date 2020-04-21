---
title: 쿠스토 CLI - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto CLI에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7443ad32b78a48f6b35be4f4b680ac6c728aedd2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524245"
---
# <a name="kusto-cli"></a>쿠스토 클리

Kusto.Cli는 Kusto에 요청을 보내고 결과를 표시하는 데 사용할 수 있는 명령줄 유틸리티입니다. Kusto.Cli는 여러 모드 중 하나에서 실행할 수 있습니다.

* *REPL 모드*: 사용자가 Kusto에 대해 실행할 쿼리 및 명령을 입력하고 도구가 결과를 표시한 다음 다음 사용자 쿼리 /명령을 기다립니다.
  ("REPL"은 "읽기/평가/인쇄/루프"를 의미합니다.)

* *실행 모드*: 사용자는 도구에 대한 명령줄 인수로 실행할 하나 이상의 쿼리 및 명령을 제공합니다. 이러한 순서대로 자동으로 실행되며 결과가 콘솔에 출력됩니다. 선택적으로 모든 입력 쿼리 및 명령을 실행하면 도구가 REPL 모드로 전환됩니다.

* *스크립트 모드*: 실행 모드와 유사하지만 파일을 통해 지정된 쿼리 및 명령 ("스크립트"라고 함).

Kusto.Cli는 주로 코드를 작성하는 데 일반적으로 필요한 Kusto 서비스에 대한 작업을 자동화하기 위해 제공됩니다(예: C# 프로그램 또는 PowerShell 스크립트).

## <a name="getting-the-tool"></a>도구 얻기

Kusto.Cli는 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)패키지의 `Microsoft.Azure.Kusto.Tools`일부로 여기에서 다운로드 할 수 있습니다.
다운로드되면 패키지의 `tools` 폴더를 대상 폴더로 추출할 수 있습니다. 추가 설치가 필요하지 않습니다(예: xcopy 설치 가능).



## <a name="running-the-tool"></a>도구 실행

Kusto.Cli를 실행하려면 하나 이상의 명령줄 인수가 필요합니다. 일반적으로 해당 인수는 도구가 연결해야 하는 Kusto 서비스에 대한 연결 문자열입니다. [Kusto 연결 문자열을](../api/connection-strings/kusto.md)참조하십시오. 명령줄 인수가 없거나 알 수 없는 인수 집합또는 `/help` 스위치를 사용하여 도구를 실행하면 콘솔로 도움말 메시지가 내보내게 됩니다.

예를 들어 다음 명령을 사용하여 Kusto.Cli를 실행하고 `help` Kusto 서비스에 연결하고 데이터베이스 컨텍스트를 `Samples` 데이터베이스에 설정합니다.

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 연결 문자열 주위에 따옴표를 사용하여 PowerShell과 같은 셸 응용 프로그램이 연결`;`문자열의 세미콜론 () 및 유사한 문자를 해석하지 못하도록 했습니다.

## <a name="command-line-arguments"></a>명령줄 인수

`Kusto.Cli.exe`*연결 스트링* [*스위치*]

*Connectionstring*
* 모든 [Kusto 연결](../api/connection-strings/kusto.md) 정보를 보유 하는 Kusto 연결 문자열입니다.
  기본값은 `net.tcp://localhost/NetDefaultDB`입니다.

`-execute:`*쿼리Or 명령*
* 지정된 경우 실행 모드에서 Kusto.Cli를 실행합니다. 지정된 쿼리 또는 명령이 실행됩니다. 이 스위치는 반복될 수 있으며, 이 경우 쿼리/명령이 모양 순서대로 순차적으로 실행됩니다.
  이 스위치는 `-script` 함께 사용할 `-scriptml`수 없습니다.

`-keepRunning:`*사용 유지 실행*
* (또는 둘 `true` 중 `false`하나)로 지정하면 모든 `-script` 값이 처리된 후 REPL 모드로 전환하거나 `-execute` 비활성화할 수 있습니다.

`-script:`*스크립트 파일*
* 지정한 경우 스크립트 모드에서 Kusto.Cli를 실행합니다. 지정된 스크립트 파일이 로드되고 쿼리 또는 명령이 순차적으로 실행됩니다.
  줄 바호는 아래에 설명된 대로 줄이 `&` 끝나거나 `&&` 조합하는 경우를 제외하고 쿼리/명령을 구분하는 데 사용됩니다.
  이 스위치는 함께 `-execute`사용할 수 없습니다.

`-scriptml:`*스크립트 파일*
* 지정한 경우 스크립트 모드에서 Kusto.Cli를 실행합니다. 지정된 스크립트 파일이 로드되고 쿼리 또는 명령이 순차적으로 실행됩니다.
  전체 스크립트 파일은 단일 쿼리 또는 명령으로 간주됩니다.
  이 스위치는 함께 `-execute`사용할 수 없습니다.

`-echo:`*인에이블에코모드*
* (또는 둘 `true` 중 `false`하나)를 지정하면 에코 모드를 활성화하거나 사용하지 않도록 설정합니다.
  에코 모드가 활성화되면 모든 쿼리 또는 명령이 출력에서 반복됩니다.

`-transcript:`*대본 파일*  
* 지정한 경우 프로그램 출력을 *TranscriptFile*에 씁니다.

`-logToConsole:`*인에이블로그토콘솔*
* (또는 둘 `true` 중 `false`하나)를 지정하면 콘솔에 프로그램 출력을 작성하거나 사용하지 않도록 설정합니다.

`-lineMode:`*인에이블라인모드*
* 이 지정하면 라인 입력 모드(기본값 또는 `true`설정시)와 블록 입력 모드(설정된 경우)를 전환합니다. `false` 줄 바선이 처리되는 방법을 결정하는 이 두 모드에 대한 설명은 아래를 참조하십시오.

**예제**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

대장과 인수 값 사이에는 공백이 없어야 합니다.

## <a name="directives"></a>지시문

Kusto.Cli는 처리를 위해 서비스로 전송되는 대신 도구에서 실행되는 여러 지시문을 지원합니다.

|지시문                      |Description|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |짧은 도움말 메시지를 받습니다.|
|`q`<br>`#quit`<br>`#exit`      |도구를 종료합니다.|
|`#a`<br>`#abort`               |도구를 중단하여 종료합니다.|
|`#clip`                        |다음 쿼리 또는 명령의 결과가 클립보드에 복사됩니다.|
|`#cls`                         |콘솔 화면을 지웁습니다.|
|`#connect`*[연결 문자열]*|다른 Kusto 서비스에 연결합니다(연결 *문자열을* 생략하면 현재 서비스가 표시됩니다.)|
|`#crp`[*Name* 이름`=` [ *값*]]   |클라이언트 요청 속성의 값을 설정합니다(또는 표시하거나 모든 값을 표시).|
|`#crp` (`-list` | `-doc`) [*접두사*]|클라이언트 요청 속성(접두사 또는 모두)을 나열합니다.|
|`#dbcontext`[*데이터베이스 이름*]  |쿼리 및 명령에서 사용하는 "컨텍스트" 데이터베이스를 *DatabaseName으로* 변경합니다(생략된 경우 현재 컨텍스트가 표시됩니다.)|
|`ke` *텍스트*                    |지정된 텍스트를 실행 중인 Kusto.Explorer 프로세스로 보냅니다.|
|`#loop`*Count* *텍스트* 수         |텍스트를 여러 번 실행합니다.|
|`#qp`[*Name* 이름`=` [ *값*]]   |쿼리 매개 변수의 값을 설정합니다(또는 쿼리 매개 변수를 표시하거나 모든 값을 표시). 시작/끝에서 단일/큰따옴표가 잘립니다.|
|`#save`*파일 이름*             |다음 쿼리 또는 명령의 결과가 표시된 CSV 파일에 저장됩니다.|
|`#script`*파일 이름*           |표시된 스크립트를 실행합니다.|
|`#scriptml`*파일 이름*         |표시된 다중 줄 스크립트를 실행합니다.|

## <a name="line-input-mode-and-block-input-mode"></a>라인 입력 모드 및 블록 입력 모드

기본적으로 Kusto.Cli는 줄 **입력 모드에서**실행됩니다: 각 줄 바호 문자는 쿼리/명령 사이의 구분 기호로 해석되고 줄은 실행을 위해 즉시 전송됩니다.

이 모드에서는 긴 쿼리 또는 명령을 여러 줄로 나누을 수 있습니다. 문자가 `&` 줄 바호 앞에 있는 줄의 마지막 문자로 나타나면 Kusto.Cli가 다음 줄을 계속 읽게 됩니다. 문자가 `&&` 줄 바호 앞에 있는 줄의 마지막 문자로 나타나면 Kusto.Cli가 줄 바선을 무시하고 다음 줄을 계속 읽게 됩니다.

또는 Kusto.Cli는 **블록 입력 모드에서**실행지원 : 명령줄 스위치를 `-lineMode:false` 사용하거나 `#blockmode`명령을 사용하여 Kusto.Cli에게 모든 줄이 이전 줄의 연속이라고 가정하도록 지시하여 쿼리와 명령이 빈 입력 줄로만 구분되도록 할 수 있습니다.

## <a name="comments"></a>주석

Kusto.Cli는 새 `//` 줄을 주석 줄로 시작하는 문자열을 해석합니다. 나머지 줄은 무시하고 다음 줄을 계속 읽습니다.

## <a name="tool-only-options"></a>공구 전용 옵션

명령                        | 영향                                                                            | 현재
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 사용 /비활성화 옵션 '타이밍': 요청이 걸린 시간을 표시                    | TRUE
#tableon|#tableoff              | 사용 설정/비활성화 옵션 'tableView': 형식 결과가 테이블로 설정                  | TRUE
#marson|#marsoff                | 활성화/비활성화 옵션 'marsView': 2차~마지막 결과 세트 표시             | FALSE
#resultson|#resultsoff          | 사용/비활성화 옵션 'outputResultsSet': 결과 세트 표시                 | TRUE
#prettyon|#prettyoff            | 사용 /비활성화 옵션 '예쁜 오류': 오류에서 불필요한 끈적 끈적한 이동을 제거          | TRUE
#markdownon|#markdownoff        | 사용 /비활성화 옵션 '마크 다운보기': 마크 다운으로 테이블 형식                   | FALSE
#progressiveon|#progressiveoff  | 사용 /비활성화 옵션 '프로그레시브보기': 요청 및 점진적 결과를 표시  | FALSE
#linemode|#blockmode            | 활성화/비활성화 옵션 'lineMode': 한 줄 입력 모드                          | TRUE

명령                              | 영향                                                                                                         | 기본
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (사용|비활성화 옵션 'crid': 요청을 보내기 전에 ClientRequestId표시)                         | FALSE
#csvheaderson|#csvheadersoff          | (사용|비활성화 옵션 'csvHeaders': CSV 출력에 헤더포함)                                            | TRUE
#focuson|#focusoff                    | (사용|옵션 '포커스'를 사용하지 않도록 설정 : 모든 여분의 보풀을 제거하고 오른쪽 물건에 초점을)                       | FALSE
#linemode|#blockmode                  | (사용|비활성화 옵션 'lineMode': 한 줄 입력 모드)                                                     | TRUE
#markdownon|#markdownoff              | (사용|비활성화 옵션 '마크다운뷰': 테이블 형식을 마크다운으로 지정)                                              | FALSE
#marson|#marsoff                      | (사용|비활성화 옵션 'marsView': 2차~마지막 결과 세트 표시)                                        | FALSE
#prettyon|#prettyoff                  | (사용|비활성화 옵션 'prettyErrors': 오류에서 불필요한 끈적 끈적한 이동을 제거)                                     | TRUE
#querystreamingon|#querystreamingoff  | (사용|비활성화 옵션 '쿼리 스트리밍': 쿼리 스트리밍 끝점 사용 (Kusto 팀만))                    | FALSE
#resultson|#resultsoff                | (사용|사용 안 함 옵션 'outputResultsSet': 결과 집합 표시)                                            | TRUE
#tableon|#tableoff                    | (사용|사용 안 함 옵션 'tableView': 형식 결과 테이블로 설정)                                             | TRUE
#timeon|#timeoff                      | (사용|비활성화 옵션 '타이밍': 요청이 걸린 시간을 표시)                                               | TRUE
#typeon|#typeoff                      | (사용|비활성화 옵션 'typeView': 테이블 보기에 각 열의 형식을 표시합니다(Streaming=true를 강제로 입력합니다))  | TRUE
#v2protocolon|#v2protocoloff          | (사용|비활성화 옵션 'v2protocol': v2 쿼리 프로토콜을 사용 하 여, v1)                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>Kusto.Cli를 사용하여 결과를 CSV로 내보내기

Kusto.Cli는 다음 쿼리 결과를 `#save`CSV 형식으로 **next** 로컬 파일로 내보내기 위해 특수 클라이언트 측 명령을 지원합니다. 예를 들어 Kusto.Cli의 다음 호출은 클러스터(데이터베이스)의 `StormEvents` `Samples` 테이블에서 `help.kusto.windows.net` 10개의 레코드를 내보냅니다.

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Kusto.Cli를 사용하여 Kusto.Explorer의 실행 중인 인스턴스를 제어합니다.

Kusto.Cli에게 컴퓨터에서 실행 중인 Kusto.Explorer의 "기본" 인스턴스와 통신하고 실행하도록 쿼리를 보낼 수 있습니다. 이것은 Kusto 쿼리의 숫자를 실행 하려는 프로그램에 대 한 매우 유용할 수 있습니다., 하지만 Kusto.Explorer 프로세스를 다시 시작 하 고 싶지 않아. 다음 예제에서 Kusto.Cli는 도움말 클러스터를 다시 쿼리를 실행하는 데 사용됩니다.

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

구문은 매우 간단합니다. `#ke`
그런 다음 쿼리는 Kusto.Cli에서 현재 클러스터/데이터베이스 집합을 사용하여 Kusto.Explorer의 기본 인스턴스로 전송됩니다.