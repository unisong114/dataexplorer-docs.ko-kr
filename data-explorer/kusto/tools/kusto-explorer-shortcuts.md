---
title: Kusto. 탐색기 바로 가기 키 (바로 가기 키)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 탐색기 바로 가기 키 (바로 가기 키)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: a5bda2079adc06ea1bca5af65d89418ef5c293f6
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264677"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Kusto 탐색기 바로 가기 키 (바로 가기 키)

## <a name="application-level"></a>응용 프로그램 수준

모든 컨텍스트에서 다음과 같은 바로 가기 키를 사용할 수 있습니다.

|바로 가기 키|Description|
|-------|-----------|
|`F1`     | 도움말을 엽니다.|
|`F11`    | 전체 뷰 모드 설정/해제|
|`Ctrl`+`F1`| 리본 모양 설정/해제 |
|`Ctrl`+`+`| 쿼리 및 데이터 결과 글꼴을 늘립니다.|
|`Ctrl`+`-`| 쿼리 및 데이터 결과 글꼴 감소|
|`Ctrl`+`0`| 쿼리 및 데이터 결과 글꼴 다시 설정|
|`Ctrl`+`1` .. `7`| 각 숫자 (1 ... 7)를 사용 하 여 쿼리 문서 패널로 전환 합니다.|
|`Ctrl`+`F2`|쿼리 편집기 패널의 머리글 이름 바꾸기|
|`Ctrl`+`N` |새 쿼리 편집기를 엽니다.|
|`Ctrl`+`O` |새 쿼리 편집기에서 쿼리 편집기 스크립트 열기|
|`Ctrl`+`W` |활성 쿼리 편집기를 닫습니다.|
|`Ctrl`+`S` |쿼리를 파일에 저장 합니다.|
|`Shift`+`F3` | 분석 보고서 갤러리를 엽니다.|
|`Shift`+`F12`| 응용 프로그램의 밝은 테마 또는 어두운 테마를 전환 합니다.|
|`Ctrl`+`Shift`+`O`|Kusto 탐색기 옵션 및 설정 대화 상자를 엽니다.|
|`Esc`|실행 중인 쿼리 취소|
|`Shift`+`F5`|실행 중인 쿼리 취소|

## <a name="query-and-results-view"></a>쿼리 및 결과 보기

다음 바로 가기 키를 쿼리 편집기에서 사용 하거나 컨텍스트가 결과 뷰에 있을 때 사용할 수 있습니다.

|바로 가기 키|Description|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|쿼리, 딥 링크 및 데이터를 클립보드에 복사 합니다.|
|`Alt`+`Shift`+`C` |쿼리를 복사 하 여 HTML 형식으로 클립보드에 연결 합니다.|
|`Alt`+`Shift`+`R` |쿼리, 딥 링크를 복사 하 고 클립보드의 데이터를 Markdown 형식으로 저장 합니다.|
|`Alt`+`Shift`+`M` |쿼리 및 딥 링크를 복사 하 여 Markdown 형식으로 클립보드를 복사 합니다.|
|`Ctrl`+`~` |Markdown 형식으로 쿼리 및 데이터를 클립보드에 복사 합니다. |
|`Ctrl`+`Shift`+`D`|데이터 뷰에서 중복 행을 숨기는 모드 설정/해제|
|`Ctrl`+`Shift`+`H`|데이터 뷰에서 빈 열을 숨기는 모드 설정/해제|
|`Ctrl`+`Shift`+`J`|데이터 뷰에서 단일 값을 사용 하 여 열 축소 모드를 설정/해제 합니다.|
|`Ctrl`+`Shift`+`A`|새 쿼리 패널에서 쿼리 분석기 도구를 엽니다.|
|`Alt`+`C`  |기존 데이터에 대해 세로 막대형 차트 렌더링|
|`Alt`+`T`  |기존 데이터로 타임 라인 차트를 렌더링 합니다.|
|`Alt`+`A`  |기존 데이터에 대해 변칙 타임 라인 차트를 렌더링 합니다.|
|`Alt`+`P`  |기존 데이터 위에 원형 차트를 렌더링 합니다.|
|`Alt`+`L`  |기존 데이터에 대해 사다리 타임 라인 차트를 렌더링 합니다.|
|`Alt`+`V`  |기존 데이터에 대해 피벗 차트를 렌더링 합니다.|
|`Ctrl`+`Shift`+`V`|기존 데이터에 대 한 타임 라인 피벗 타임 라인 표시|
|`Ctrl`+`F9`  | `show only matching rows` / `highlight matching rows` 데이터 표에서 클라이언트 텍스트 검색 () 동작에 대 한 모드를 설정/해제 `Ctrl` + `F` 합니다. |
|`Ctrl`+`F10` |선택한 행이 속성 표로 표시 되는 세부 정보 패널을 표시 합니다.|
|`Ctrl`+`F`  | 현재 포커스가 있는 패널에 대 한 검색 상자를 표시 합니다. `Connetions`, `Data Results` 및 `Query Editor` 패널에서 지원 됨|
|`Ctrl`+`Tab`| 쿼리 편집기 문서 선택기 대화 상자를 표시 합니다. `Ctrl`다음을 사용 하 여 문서를 유지 하 고 전환할 수 있습니다.`Tab` |
|`Ctrl`+`J`|결과 패널의 모양을 설정/해제 합니다.|
|`Ctrl`+`E`|다음 주기에서 쿼리 편집기와 결과 패널의 모양을 설정/해제 합니다.`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|다음 주기에서 쿼리 편집기와 결과 패널의 모양을 설정/해제 합니다.`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | 결과 패널에 초점을 맞춘 |
|`Ctrl`+`Shift`+`T` | 연결 패널에 초점을 맞춘 |
|`Ctrl`+`Shift`+`Y` | 쿼리 편집기에 초점을 맞춘 |
|`Ctrl`+`Shift`+`P` | 차트 패널에 초점을 맞춘 |
|`Ctrl`+`Shift`+`I` | 쿼리 정보 패널에 초점을 맞춘 |
|`Ctrl`+`Shift`+`S` | 쿼리 통계 패널에 초점을 맞춘 |
|`Ctrl`+`Shift`+`K` | 오류 패널에 초점을 맞춘 |
|`Alt`+`Ctrl`+`L`|현재 연결 컨텍스트를 쿼리 편집기로 잠가 연결 패널에서 선택 된 행을 변경 해도 쿼리 편집기 컨텍스트에는 영향을 주지 않습니다. |

## <a name="results-table-viewer"></a>결과 테이블 뷰어

결과 뷰 (테이블)가 활성 키보드 포커스에 있으면 다음과 같은 바로 가기 키를 사용할 수 있습니다.

|바로 가기 키|Description|
|-----------|-----------|
|`Ctrl`+`Q` |현재 열 표시 상황에 맞는 메뉴|
|`Ctrl`+`S` |현재 열 정렬 설정/해제|
|`Ctrl`+`U` |클라이언트 쪽 필터링을 사용 하 여 현재 열 값을 보여 주는 패널을 엽니다.|
|`Ctrl`+`F` | 결과의 검색 상자를 표시 합니다.|
|`Ctrl`+`F3`| `show only matching rows` / `highlight matching rows` 데이터 표에서 클라이언트 텍스트 검색 () 동작에 대 한 모드를 설정/해제 `Ctrl` + `F` 합니다. |

## <a name="query-editor"></a>쿼리 편집기

쿼리 편집기에서 쿼리를 편집할 때 다음과 같은 바로 가기 키를 사용할 수 있습니다.

|바로 가기 키|Description|
|-------|-----------|
|`F1`|커서가 연산자나 함수를 가리키면 연산자 또는 함수에 대 한 정보가 포함 된 도움말 창이 열립니다. 도움말 항목이 표시 되지 않으면 도움말 URL을 엽니다.|
|`F5`|현재 선택한 쿼리 실행|
|`Shift`+`Enter`|현재 선택한 쿼리 실행|
|`F8`|로컬 캐시에서 쿼리 결과를 인출 합니다. 결과가 표시 되지 않는 경우 현재 선택 되어 있는 쿼리를 실행 합니다.|
|`F6`|모드에서 현재 선택한 쿼리 실행 `Progressive Results`|
|`Ctrl`+`F5` | 선택한 쿼리의 결과 미리 보기 (몇 개의 결과와 총 개수 표시)|
|`Ctrl`+`Shift`+`Space`| 쿼리에 필터로 데이터 셀 선택 항목 삽입|
|`Ctrl`+`Space`| IntelliSense 규칙 검사를 강제로 실행 합니다. 사용할 수 있는 옵션은 일치 하는 모든 규칙에 표시 됩니다. |
|`Ctrl`+`Enter`| `pipe`기호를 추가 하 고 새 줄로 이동 합니다.|
|`Ctrl`+`Z`| 실행 취소 |
|`Ctrl`+`Y`| 다시 실행 |
|`Ctrl`+`L`| 현재 줄을 삭제 합니다.|
|`Ctrl`+`D`| 현재 줄을 삭제 합니다.| 
|`Ctrl`+`F`| `Find and Replace`대화 상자 열기 |
|`Ctrl`+`G`| `Go-to line`대화 상자 열기 |
|`Ctrl`+`F8` | 지난 3 일간 쿼리 표시 |
|`Ctrl`+ 대괄호 | 커서가 대괄호 기호에 있으면,, `(` , `)` `[` `]` , `{` , `}` -커서를 일치 하는 여는 중괄호 또는 닫는 괄호로 이동 합니다. |
|`Ctrl`+`Shift`+`Q` | 현재 쿼리 꾸밀 |
|`Ctrl`+`Shift`+`L` | 현재 쿼리 또는 선택 항목을 소문자로 만들기 |
|`Ctrl`+`Shift`+`U` |  현재 쿼리 또는 선택 항목을 대문자로 만들기 |
|`Ctrl`+`Mouse wheel up`| 쿼리 편집기의 글꼴을 늘립니다.|
|`Ctrl`+`Mouse wheel down`| 쿼리 편집기의 글꼴을 줄입니다.|
|`Alt`+`P` | 쿼리 매개 변수 대화 상자 열기 |
|`F2`| 편집기 대화 상자에서 현재 줄/선택한 텍스트 열기 |
|`Ctrl`+`F6`| KQL 정적 쿼리 분석을 실행 하 여 일반적인 문제를 검색 합니다. |
|`F12`| 기호 정의로 이동 합니다. |
|`Ctrl`+`F12`| 현재 기호의 모든 참조 찾기 |
|`Alt`+`Home`| 기호 정의로 이동 합니다. |
|`Alt`+`Ctrl`+`M`| 현재 선택한 리터럴 또는 테이블 형식 식을 let 문으로 추출 합니다. |
|`Ctrl`+`.`| 현재 선택한 리터럴 또는 테이블 형식 식을 let 문으로 추출 합니다. |
|`Ctrl`+`R`, `Ctrl`+`R` | 현재 기호의 이름을 바꿉니다. |
|`Ctrl`+`K`, `Ctrl`+`D` | 현재 타임 스탬프를 datetime 리터럴로 삽입 합니다. |
|`Ctrl`+`K`, `Ctrl`+`R` | `range x from 1 to 1 step 1`코드 조각 삽입 |
|`Ctrl`+`K`, `Ctrl`+`C` | 현재 줄 또는 선택한 줄 주석 처리 |
|`Ctrl`+`K`, `Ctrl`+`F` | 현재 쿼리 꾸밀 |
|`Ctrl`+`K`, `Ctrl`+`V` | 현재 쿼리를 복제 합니다 (현재 쿼리 문서의 끝에 추가). |
|`Ctrl`+`K`, `Ctrl`+`U` | 현재 줄 또는 선택한 줄의 주석 처리를 제거 합니다. |
|`Ctrl`+`K`, `Ctrl`+`S` | 현재 줄 이나 선택한 줄을 여러 줄 문자열 리터럴로 전환 |
|`Ctrl`+`K`, `Ctrl`+`M` | 여러 줄 문자열 리터럴 표시 제거 (역방향 `Ctrl` + `K` , `Ctrl` + `S` ) |
|`Ctrl`+`M`, `Ctrl`+`M` | 현재 쿼리의 개요 확장 설정/해제 |
|`Ctrl`+`M`, `Ctrl`+`L` | 문서에 있는 모든 쿼리의 개요 확장 설정/해제 |

## <a name="json-viewer"></a>JSON 뷰어

결과 JSON 뷰어 내에서 다음 바로 가기 키를 사용할 수 있습니다.
결과 뷰 셀에서 JSON과 유사한 값을 두 번 클릭 하면 표시 됩니다.

|바로 가기 키|Description|
|-------|-----------|
|`Ctrl`+`Up Arrow`|부모로 이동|
|`Ctrl`+`Right Arrow`|현재 노드 확장 (한 수준)|
|`Ctrl`+`Left Arrow`|현재 노드 축소 (한 수준)|
|`Ctrl`+`.`|현재 노드의 확장을 설정/해제 합니다 (확장/축소 된 모든 자식 수준).
|`Ctrl`+`Shift`+`.`|현재 노드 부모의 확장 전환 (확장/축소 된 모든 자식 수준)|

## <a name="connection-panel"></a>연결 패널

결과 JSON 뷰어 내에서 다음 바로 가기 키를 사용할 수 있습니다.
결과 뷰 셀에서 JSON과 유사한 값을 두 번 클릭 하면 표시 됩니다.

|바로 가기 키|Description|
|-------|-----------|
|`Ctrl`+`Up`화살표나|부모로 이동|
|`Ctrl`+`Right`화살표나|현재 노드 확장 (한 수준)|
|`Ctrl`+`Left`화살표나|현재 노드 축소 (한 수준)|
|`Ctrl`+`Shift`+`L`|모든 수준 축소|
|`Ctrl`+`R`|현재 선택한 연결 새로 고침|
|`Insert`|새 연결 추가|
|`Del`|현재 연결 삭제|
|`Ctrl`+`E`|현재 선택한 연결 편집|
|`Ctrl`+`T`|현재 선택한 연결을 사용 하 여 새 쿼리 편집기 열기|

## <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

리본 메뉴에서 사용할 수 있는 바로 가기 키는 다음과 같습니다 `Monitoring` .

|바로 가기 키|Description|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|클러스터 진단 흐름 실행|
