---
title: Kusto CLI-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto CLI에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: adc606c787ab20f228a9fbd132d1b82660aa57c6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512489"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto. Cli는 Kusto에 요청을 보내고 결과를 표시 하는 데 사용 되는 명령줄 유틸리티입니다. 여러 모드 중 하나로 실행할 수 있습니다.

* *REPL 모드*: 사용자가 쿼리 및 명령을 입력 하면 도구에서 결과가 표시 되 고 다음 사용자 쿼리/명령이 기다립니다 됩니다.
  "REPL"은 "읽기/평가/인쇄/루프"를 나타냅니다.

* *실행 모드*: 사용자가 명령줄 인수로 실행할 하나 이상의 쿼리와 명령을 입력 합니다. 인수는 자동으로 순서 대로 실행 되며 그 결과는 콘솔에 출력 됩니다. 모든 입력 쿼리 및 명령이 실행 된 후에는 필요에 따라 도구는 REPL 모드로 전환 됩니다.

* *스크립트 모드*: 실행 모드와 비슷하지만 "스크립트" 파일을 통해 쿼리 및 명령을 지정 합니다.

일반적으로 코드를 작성 해야 하는 Kusto 서비스에 대 한 작업을 자동화 하기 위해 kusto. Cli가 기본적으로 제공 됩니다. 예를 들어 c # 프로그램 또는 PowerShell 스크립트입니다.

## <a name="get-the-tool"></a>도구 가져오기

Kusto. Cli는 `Microsoft.Azure.Kusto.Tools` [다운로드할 수](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)있는 NuGet 패키지의 일부입니다.
다운로드 되 면 패키지의 폴더를 `tools` 대상 폴더로 추출 합니다. Xcopy가 설치 가능 하므로 추가 설치는 필요 하지 않습니다.

## <a name="run-the-tool"></a>도구 실행

Kusto Cli를 실행 하려면 명령줄 인수가 하나 이상 필요 합니다. 일반적으로 해당 인수는 도구에서 연결 해야 하는 Kusto 서비스에 대 한 연결 문자열입니다.
자세한 내용은 [Kusto 연결 문자열](../api/connection-strings/kusto.md)을 참조 하세요. 명령줄 인수 없이 도구를 실행 하거나 알 수 없는 인수 집합을 사용 하거나 스위치를 사용 하는 경우 `/help` 콘솔에 도움말 메시지가 표시 됩니다.

예를 들어 다음 명령을 사용 하 여 Kusto. Cli를 실행 합니다. 이 명령은 `help` kusto 서비스에 연결 하 고 데이터베이스 컨텍스트를 데이터베이스에 설정 합니다 `Samples` .

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 연결 문자열 주위에 큰따옴표를 사용 하 여 PowerShell과 같은 셸 응용 프로그램에서 세미콜론 ( `;` ) 및 유사한 문자를 잘못 해석 하는 것을 방지 합니다.

## <a name="command-line-arguments"></a>명령줄 인수

`Kusto.Cli.exe`*ConnectionString* [*스위치*]

*ConnectionString*
* 모든 Kusto 연결 정보를 포함 하는 [Kusto 연결 문자열](../api/connection-strings/kusto.md) 입니다.
  기본값은 `net.tcp://localhost/NetDefaultDB`입니다.

`-execute:`*QueryOrCommand*
* 지정 된 경우 실행 모드에서 Kusto. Cli를 실행 하 고 지정 된 쿼리 또는 명령이 실행 됩니다. 이 스위치는 반복 될 수 있으며 쿼리/명령은 모양에 따라 순차적으로 실행 됩니다.
  이 스위치는 또는와 함께 사용할 수 없습니다 `-script` `-scriptml` .

`-keepRunning:`*EnableKeepRunning*
* 또는로 지정 된 경우, 또는 `true` `false` 값이 모두 처리 된 후에는 REPL 모드를 사용 하거나 사용 하지 않도록 설정 `-script` `-execute` 합니다.

`-script:`*ScriptFile*
* 지정 하는 경우 스크립트 모드에서 Kusto. Cli를 실행 합니다. 지정 된 스크립트 파일이 로드 되 고 쿼리 또는 명령이 순차적으로 실행 됩니다.
  줄바꿈는 아래에 설명 된 대로 줄이 또는 조합으로 끝나는 경우를 제외 하 고 쿼리/명령을 구분 하는 데 사용 됩니다 `&` `&&` .
  이 스위치는와 함께 사용할 수 없습니다 `-execute` .

`-scriptml:`*ScriptFile*
* 지정 하는 경우 스크립트 모드에서 Kusto. Cli를 실행 합니다. 지정 된 스크립트 파일이 로드 되 고 쿼리 또는 명령이 순차적으로 실행 됩니다.
  전체 스크립트 파일은 단일 쿼리 또는 명령으로 간주 됩니다.
  이 스위치는와 함께 사용할 수 없습니다 `-execute` .

`-echo:`*EnableEchoMode*
* 또는로 지정 하면 `true` `false` 에코 모드를 사용 하거나 사용 하지 않도록 설정 합니다.
  Echo 모드를 사용 하도록 설정 하면 모든 쿼리 또는 명령이 출력에 반복 됩니다.

`-transcript:`*TranscriptFile*  
* 지정 하면 프로그램 출력을 *TranscriptFile*에 씁니다.

`-logToConsole:`*EnableLogToConsole*
* 또는로 지정 된 경우 `true` `false` 콘솔에서 프로그램 출력 표시를 사용 하거나 사용 하지 않도록 설정 합니다.

`-lineMode:`*EnableLineMode*
* 지정 하는 경우 기본 선 입력 모드를로 설정 하 고를로 설정 하면이를 `true` 차단 입력 모드로 전환 합니다 `false` . 줄바꿈 처리 방법을 결정 하는 이러한 두 모드에 대 한 설명은 아래를 참조 하십시오.

**예제**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

> [!NOTE] 
> 콜론과 인수 값 사이에 공백이 없어야 합니다.

## <a name="directives"></a>지시문

Kusto. Cli는 처리를 위해 서비스에 전송 하는 대신 도구에서 많은 지시문을 실행 합니다.

|지시문                      |설명|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |짧은 도움말 메시지 가져오기|
|`q`<br>`#quit`<br>`#exit`      |도구 종료|
|`#a`<br>`#abort`               |Abortively 도구 종료|
|`#clip`                        |다음 쿼리 또는 명령의 결과가 클립보드로 복사 됩니다.|
|`#cls`                         |콘솔 화면 지우기|
|`#connect`*[ConnectionString*]|다른 Kusto 서비스에 연결 합니다 ( *ConnectionString* 이 생략 되 면 현재 항목 표시 됨).|
|`#crp`[*이름* [ `=` *값*]]   |클라이언트 요청 속성의 값을 설정 하거나, 표시 하거나, 모든 값을 표시 합니다.|
|`#crp`( `-list` \| `-doc` ) [*Prefix*]|클라이언트 요청 속성, 접두사 또는 모두 나열|
|`#dbcontext`[*DatabaseName*]  |쿼리 및 명령에 사용 되는 "context" 데이터베이스를 *DatabaseName*으로 변경 합니다. 생략 하면 현재 컨텍스트가 표시 됩니다.|
|`ke` *텍스트*                    |실행 중인 Kusto 탐색기 프로세스에 지정 된 텍스트를 보냅니다.|
|`#loop`*개수* *텍스트*         |텍스트를 여러 번 실행 합니다.|
|`#qp`[*이름* [ `=` *값*]]   |쿼리 매개 변수의 값을 설정 하거나 표시 하거나 모든 값을 표시 합니다. 시작/끝의 단일/큰따옴표가 잘립니다.|
|`#save`*파일 이름*             |다음 쿼리 또는 명령의 결과는 지정 된 CSV 파일에 저장 됩니다.|
|`#script`*파일 이름*           |표시 된 스크립트를 실행 합니다.|
|`#scriptml`*파일 이름*         |표시 된 여러 줄 스크립트를 실행 합니다.|

## <a name="line-input-mode-and-block-input-mode"></a>줄 입력 모드 및 블록 입력 모드

기본적으로 Kusto. Cli는 **선 입력 모드**에서 실행 됩니다. 각 줄 바꿈 문자는 쿼리/명령 사이에 구분 기호로 해석 되 고 줄은 실행을 위해 즉시 전송 됩니다.

이 모드에서는 긴 쿼리 또는 명령을 여러 줄로 나눌 수 있습니다. `&`줄의 마지막 문자로 서 줄 바꿈 앞에 오는 문자는 Kusto Cli를 통해 다음 줄을 계속 읽습니다. `&&`줄의 마지막 문자로 서 줄 바꿈 전의 문자는 Kusto .로 인해 줄 바꿈이 무시 되 고 다음 줄을 계속 읽습니다.

Kusto. Cli는 **블록 입력 모드**에서의 실행도 지원 합니다. 명령줄 스위치 `-lineMode:false` 를 사용 하거나 지시어를 사용 하 여 `#blockmode` 모든 줄이 이전 줄의 연속으로 가정 하도록 Kusto. Cli에 지시할 수 있습니다. 그러면 쿼리 및 명령이 빈 입력 줄로만 구분 됩니다.

## <a name="comments"></a>주석

Kusto. Cli는 `//` 새 줄을 주석 줄로 시작 하는 문자열을 해석 합니다. 나머지 줄을 무시 하 고 다음 줄을 계속 읽습니다.

## <a name="tool-only-options"></a>도구 전용 옵션

명령                        | 효과                                                                            | 중인
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 사용/사용 안 함 옵션 `timing` : 걸린 시간 요청 표시                    | TRUE
#tableon|#tableoff              | 설정/해제 옵션 `tableView` : 결과 집합을 테이블로 서식 지정                  | TRUE
#marson|#marsoff                | 설정/해제 옵션 `marsView` : 두 번째-마지막 결과 집합을 표시 합니다.          | FALSE
#resultson|#resultsoff          | 설정/해제 옵션 `outputResultsSet` : 결과 집합을 표시 합니다.                 | TRUE
#prettyon|#prettyoff            | 사용/사용 안 함 옵션 `prettyErrors` : 정리 오류                             | TRUE
#markdownon|#markdownoff        | 설정/해제 옵션 `markdownView` : MarkDown로 테이블 서식 지정                   | FALSE
#progressiveon|#progressiveoff  | 설정/해제 옵션 `progressiveView` : 프로그레시브 결과를 요청 하 고 표시 합니다.  | FALSE
#linemode|#blockmode            | 사용/사용 안 함 옵션 `lineMode` : 한 줄 입력 모드                          | TRUE

명령                              | 효과                                                                                                         | 기본값
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (사용|사용 안 함 옵션 `crid` : 요청을 보내기 전에 ClientRequestId를 표시 합니다.                          | FALSE
#csvheaderson|#csvheadersoff          | (사용|disable 옵션 `csvHeaders` : CSV 출력에 헤더 포함)                                            | TRUE
#focuson|#focusoff                    | (사용|disable option `focus` : 모든 추가 유익을 제거 하 고 올바른 기능에 집중 합니다.                        | FALSE
#linemode|#blockmode                  | (사용|disable 옵션 `lineMode` : 한 줄 입력 모드)                                                      | TRUE
#markdownon|#markdownoff              | (사용|disable 옵션 `markdownView` : MarkDown로 테이블 서식 지정)                                              | FALSE
#marson|#marsoff                      | (사용|disable 옵션 `marsView` : 두 번째-마지막 결과 집합을 표시 합니다.                                      | FALSE
#prettyon|#prettyoff                  | (사용|사용 안 함 옵션 `prettyErrors` : 정리 오류)                                                        | TRUE
#querystreamingon|#querystreamingoff  | (사용|사용 안 함 옵션 `queryStreaming` : queryStreaming 끝점 사용 (Kusto 팀에만 해당))                    | FALSE
#resultson|#resultsoff                | (사용|disable 옵션 `outputResultsSet` : 결과 집합을 표시 합니다.                                            | TRUE
#tableon|#tableoff                    | (사용|disable 옵션 `tableView` : 결과 집합을 테이블로 서식 지정                                              | TRUE
#timeon|#timeoff                      | (사용|사용 안 함 옵션 `timing` : 요청이 걸린 시간을 표시 합니다.                               | TRUE
#typeon|#typeoff                      | (사용|disable option `typeView` : 테이블 뷰에 각 열의 유형을 표시 합니다. 강제 스트리밍 = true)| TRUE
#v2protocolon|#v2protocoloff          | (사용|disable option `v2protocol` : v1이 아닌 v2 쿼리 프로토콜 사용                                        | TRUE

## <a name="use-kustocli-to-export-results-as-csv"></a>Kusto. Cli를 사용 하 여 결과를 CSV로 내보내기

Kusto. Cli에는 `#save` **다음** 쿼리 결과를 CSV 형식의 로컬 파일로 내보내는 특수 한 클라이언트 쪽 명령이 있습니다. 예를 들어 다음 줄은 테이블에서 10 개의 레코드 `StormEvents` `help.kusto.windows.net` 를 클러스터 `Samples` 데이터베이스로 내보냅니다.

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="use-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Kusto Cli를 사용 하 여 실행 중인 Kusto 탐색기 인스턴스를 제어 합니다.

Kusto. Cli를 사용 하 여 컴퓨터에서 실행 중인 "기본" 인스턴스와 통신 하 고 쿼리를 전송 합니다. 이 메커니즘은 많은 쿼리를 실행 하려는 프로그램에 유용할 수 있지만, 계속 해 서 Kusto 탐색기 프로세스를 시작 하지 않으려고 합니다. 다음 예제에서 Kusto. Cli는 도움말 클러스터에 대해 쿼리를 실행 하는 데 사용 됩니다.

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

구문은 이며 `#ke` , 그 뒤에 공백 및 실행할 쿼리가 있습니다.
그런 다음이 쿼리는 Kusto. 탐색기의 주 인스턴스 (있는 경우)로 전송 된 후 현재 클러스터/데이터베이스가
 
