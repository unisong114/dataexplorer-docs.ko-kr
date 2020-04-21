---
title: Kusto.Explorer 키보드 단축키(단축키) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto.Explorer 키보드 단축키(단축키)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: 81d99d14831905681c2dbbc45aea4b63134a06a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523922"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Kusto.Explorer 키보드 단축키(단축키)

## <a name="application-level"></a>응용 프로그램 수준

다음 키보드 단축키는 모든 컨텍스트에서 사용할 수 있습니다.

|핫 키|Description|
|-------|-----------|
|`F1`     | 도움말을 엽니다.|
|`F11`    | 전체 보기 모드 전환|
|`Ctrl`+`F1`| 리본 모양 전환 |
|`Ctrl`+`+`| 쿼리 및 데이터 결과 글꼴 증가|
|`Ctrl`+`-`| 쿼리 및 데이터 결과 글꼴 감소|
|`Ctrl`+`0`| 쿼리 및 데이터 결과 글꼴 재설정|
|`Ctrl`+`1` .. `7`| 각 번호(1..7)를 사용하여 문서 패널 쿼리로 전환|
|`Ctrl`+`F2`|쿼리 편집기 패널의 헤더 이름 바꾸기|
|`Ctrl`+`N` |새 쿼리 편집기 열기|
|`Ctrl`+`O` |새 쿼리 편집기에서 쿼리 편집기 스크립트 열기|
|`Ctrl`+`W` |활성 쿼리 편집기 닫기|
|`Ctrl`+`S` |쿼리를 파일에 저장합니다.|
|`Shift`+`F3` | 분석 보고서 갤러리 를 엽니다.|
|`Shift`+`F12`| 응용 프로그램의 밝은/어두운 테마를 전환합니다.|
|`Ctrl`+`Shift`+`O`|Kusto.Explorer 옵션 및 설정 대화 상자 가 열립니다.|
|`Esc`|실행 중인 쿼리 취소|
|`Shift`+`F5`|실행 중인 쿼리 취소|

## <a name="query-and-results-view"></a>쿼리 및 결과 보기

쿼리 편집기에서 쿼리를 편집하거나 컨텍스트가 결과 보기에 있을 때 다음 키보드 단축키를 사용할 수 있습니다.

|핫 키|Description|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|쿼리, 딥 링크 및 데이터를 클립보드에 복사합니다.|
|`Alt`+`Shift`+`C` |쿼리를 복사하고 클립보드를 HTML 형식으로 딥 링크합니다.|
|`Alt`+`Shift`+`R` |마크다운 형식으로 클립보드를 쿼리, 딥 링크 및 데이터 복사|
|`Alt`+`Shift`+`M` |마크다운 형식으로 쿼리 를 복사하고 클립보드를 딥 링크합니다.|
|`Ctrl`+`~` |마크다운 형식으로 클립보드에 쿼리 및 데이터 복사 |
|`Ctrl`+`Shift`+`D`|데이터 뷰에서 중복 행을 숨기는 전환 모드|
|`Ctrl`+`Shift`+`H`|데이터 뷰에서 빈 열을 숨기는 전환 모드|
|`Ctrl`+`Shift`+`J`|데이터 뷰에서 단일 값으로 열 축소 모드 전환|
|`Ctrl`+`Shift`+`A`|새 쿼리 패널에서 쿼리 분석기 도구를 엽니다.|
|`Alt`+`C`  |기존 데이터에 대해 열차트 렌더링|
|`Alt`+`T`  |기존 데이터보다 타임라인 차트 렌더링|
|`Alt`+`A`  |기존 데이터에 이상 징후 타임라인 차트 렌더링|
|`Alt`+`P`  |기존 데이터 위에 원형 차트 렌더링|
|`Alt`+`L`  |기존 데이터 위에 래더 타임라인 차트 렌더링|
|`Alt`+`V`  |기존 데이터에 대한 피벗 차트 렌더링|
|`Ctrl`+`Shift`+`V`|기존 데이터에 대한 타임라인 피벗 표시|
|`Ctrl`+`F9`  | 데이터 그리드에서 클라이언트 텍스트 `show only matching rows` / `highlight matching rows` 검색`Ctrl`+`F`() 동작에 대한 모드를 전환합니다. |
|`Ctrl`+`F10` |세부 정보 패널 표시 - 선택한 행이 속성 표로 표시되는 위치|
|`Ctrl`+`F`  | 현재 포커스가 있는 패널에 대한 검색 상자를 표시합니다. 에서 `Connetions` `Data Results`지원됩니다. `Query Editor`|
|`Ctrl`+`Tab`| 쿼리 편집기 문서 선택기 대화 상자를 표시합니다. 문서 간에 `Ctrl` 고정하고`Tab` |
|`Ctrl`+`J`|결과 패널의 모양을 전환합니다.|
|`Ctrl`+`E`|다음 주기에서 쿼리 편집기 및 결과 패널의 모양을 전환합니다.`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|다음 주기에서 쿼리 편집기 및 결과 패널의 모양을 전환합니다.`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | 결과 패널에 초점 |
|`Ctrl`+`Shift`+`T` | 연결 패널에 초점을 맞춥니다. |
|`Ctrl`+`Shift`+`Y` | 쿼리 편집기에 중점을 둡니다. |
|`Ctrl`+`Shift`+`P` | 차트 패널에 초점 |
|`Ctrl`+`Shift`+`I` | 쿼리 정보 패널에 중점을 둡니다. |
|`Ctrl`+`Shift`+`S` | 쿼리 통계 패널에 초점 |
|`Ctrl`+`Shift`+`K` | 오류 패널에 초점을 맞춥니다. |
|`Alt`+`Ctrl`+`L`|현재 연결 컨텍스트를 쿼리 편집기로 잠그므로 Connetion 패널에서 선택한 행을 변경하면 쿼리 편집기 컨텍스트에 영향을 주지 않습니다. |

## <a name="results-table-viewer"></a>결과 표 뷰어

다음 키보드 단축키는 결과 보기(표)가 활성 키보드 포커스에 있을 때 사용할 수 있습니다.

|핫 키|Description|
|-----------|-----------|
|`Ctrl`+`Q` |현재 열 컨텍스트 메뉴 표시|
|`Ctrl`+`S` |현재 열 정렬 전환|
|`Ctrl`+`U` |클라이언트 측 필터링을 통해 현재 열 값을 표시하는 패널을 엽니다.|
|`Ctrl`+`F` | 결과에 대한 검색 상자 표시|
|`Ctrl`+`F3`| 데이터 그리드에서 클라이언트 텍스트 `show only matching rows` / `highlight matching rows` 검색`Ctrl`+`F`() 동작에 대한 모드를 전환합니다. |

## <a name="query-editor"></a>쿼리 편집기

쿼리 편집기에서 쿼리를 편집할 때 다음 키보드 단축키를 사용할 수 있습니다.

|핫 키|Description|
|-------|-----------|
|`F1`|커서가 연산자 또는 함수를 가리키면 연산자 또는 기능에 대한 정보가 있는 도움말 창이 열립니다. 도움말 항목이 없는 경우 도움말 URL을 엽니다.|
|`F5`|현재 선택된 쿼리 실행|
|`Shift`+`Enter`|현재 선택된 쿼리 실행|
|`F8`|로컬 캐시에서 쿼리 결과를 가져옵니다. 결과가 없는 경우 - 현재 선택된 쿼리 실행|
|`F6`|`Progressive Results` 모드에서 현재 선택된 쿼리 실행|
|`Ctrl`+`F5` | 선택한 쿼리의 결과 미리 보기(몇 가지 결과 및 총 개수 표시)|
|`Ctrl`+`Shift`+`Space`| 데이터 셀 선택을 쿼리에 필터로 삽입합니다.|
|`Ctrl`+`Space`| 강제 인텔리센스 규칙 확인. 일치하는 규칙에 가능한 옵션이 표시됩니다. |
|`Ctrl`+`Enter`| 기호를 추가하고 `pipe` 새 줄로 이동|
|`Ctrl`+`Z`| 실행 취소 |
|`Ctrl`+`Y`| 다시 하기 |
|`Ctrl`+`L`| 현재 줄 삭제|
|`Ctrl`+`D`| 현재 줄 삭제| 
|`Ctrl`+`F`| 대화 `Find and Replace` 상자 열기 |
|`Ctrl`+`G`| 대화 `Go-to line` 상자 열기 |
|`Ctrl`+`F8` | 지난 3일 간 내 쿼리 표시 |
|`Ctrl`+ 브래킷 | 커서가 대괄호 기호에 `(` `)` 있을 `[` `]` 때: `}` " , `{` " 및 커서가 일치하는 열기 또는 닫는 대괄호로 커서를 이동합니다. |
|`Ctrl`+`Shift`+`Q` | 현재 쿼리 예쁘게 표시 |
|`Ctrl`+`Shift`+`L` | 현재 쿼리 또는 선택 소문자 만들기 |
|`Ctrl`+`Shift`+`U` |  현재 쿼리 또는 선택 대/소문자 만들기 |
|`Ctrl`+`Mouse wheel up`| 쿠퍼리 편집기의 글꼴 증가| 
|`Ctrl`+`Mouse wheel down`| 쿼리 편집기의 글꼴 을 줄입니다.|
|`Alt`+`P` | 쿼리 매개 변수 대화 상자 열기 |
|`F2`| 편집기 대화 상자에서 현재 줄 열기 / 선택한 텍스트 |
|`Ctrl`+`F6`| KQL 정적 쿼리 분석을 실행하여 일반적인 문제를 검색합니다. |
|`F12`| 기호의 정의로 이동 |
|`Ctrl`+`F12`| 현재 기호의 모든 참조 찾기 |
|`Alt`+`Home`| 기호의 정의로 이동 |
|`Alt`+`Ctrl`+`M`| let 문으로 현재 선택한 리터럴 또는 테이블 형식 식 추출 |
|`Ctrl`+`.`| let 문으로 현재 선택한 리터럴 또는 테이블 형식 식 추출 |
|`Ctrl`+`R`, `Ctrl`+`R` | 현재 기호의 이름 바꾸기 |
|`Ctrl`+`K`, `Ctrl`+`D` | 현재 타임스탬프를 분리 시간 리터럴로 삽입합니다. |
|`Ctrl`+`K`, `Ctrl`+`R` | `range x from 1 to 1 step 1` 스니펫 삽입 |
|`Ctrl`+`K`, `Ctrl`+`C` | 현재 선 또는 선택한 선 에 주석 을 주석 |
|`Ctrl`+`K`, `Ctrl`+`F` | 현재 쿼리 예쁘게 표시 |
|`Ctrl`+`K`, `Ctrl`+`V` | 현재 쿼리 중복(현재 쿼리 문서의 끝에 부속) |
|`Ctrl`+`K`, `Ctrl`+`U` | 현재 선 또는 선택한 선의 주석 해제 |
|`Ctrl`+`K`, `Ctrl`+`S` | 현재 선 또는 선택한 줄을 다중 줄 문자열 리터럴로 전환 |
|`Ctrl`+`K`, `Ctrl`+`M` | 멀티 라인 교반 리터럴 마크 `Ctrl` + `K`제거 `Ctrl` + `S`(의 역방향) |
|`Ctrl`+`M`, `Ctrl`+`M` | 현재 쿼리의 확장을 개요로 전환 |
|`Ctrl`+`M`, `Ctrl`+`L` | 문서의 모든 쿼리 확장 개요 전환 |

## <a name="json-viewer"></a>JSON 뷰어

다음 키보드 쇼컷은 결과 JSON 뷰어 내에서 사용할 수 있습니다(결과 보기 셀에서 JSON과 같은 값을 한 번 클릭하면 표시됨).

|핫 키|Description|
|-------|-----------|
|`Ctrl`+`Up Arrow`|상위로 이동|
|`Ctrl`+`Right Arrow`|현재 노드 확장(한 수준)|
|`Ctrl`+`Left Arrow`|현재 노드 축소(한 수준)|
|`Ctrl`+`.`|현재 노드의 확장 전환(모든 하위 수준 확장/축소)
|`Ctrl`+`Shift`+`.`|현재 노드 부모의 확장 전환(모든 자식 수준 확장/축소)|

## <a name="connection-panel"></a>연결 패널

다음 키보드 쇼컷은 결과 JSON 뷰어 내에서 사용할 수 있습니다(결과 보기 셀에서 JSON과 같은 값을 한 번 클릭하면 표시됨).

|핫 키|Description|
|-------|-----------|
|`Ctrl`+`Up`화살표|상위로 이동|
|`Ctrl`+`Right`화살표|현재 노드 확장(한 수준)|
|`Ctrl`+`Left`화살표|현재 노드 축소(한 수준)|
|`Ctrl`+`Shift`+`L`|모든 레벨 축소|
|`Ctrl`+`R`|현재 선택한 연결 새로 고침|
|`Insert`|새 연결 추가|
|`Del`|현재 연결 삭제|
|`Ctrl`+`E`|현재 선택한 연결 편집|
|`Ctrl`+`T`|현재 선택된 연결을 사용하여 새 쿼리 편집기 열기|

## <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

다음 키보드 단축키는 리본에서 `Monitoring` 사용할 수 있습니다.

|핫 키|Description|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|클러스터 진단 흐름 실행|