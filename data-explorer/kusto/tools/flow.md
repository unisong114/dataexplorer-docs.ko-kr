---
title: 마이크로소프트 흐름 Azure 쿠스토 커넥터 (미리 보기) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Microsoft Flow Azure Kusto 커넥터(미리 보기)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: c4e732afb9e6165f803b9dc7e801b4e2be4c2588
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504100"
---
# <a name="microsoft-flow-azure-kusto-connector-preview"></a>마이크로소프트 흐름 Azure 쿠스토 커넥터 (미리 보기)

Microsoft Flow Azure Kusto 커넥터를 사용하면 Microsoft Flow를 사용하여 예약되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행할 [수 있습니다.](https://flow.microsoft.com/)

일반적인 사용 시나리오는 다음과 같습니다.

* 테이블및차트가 포함된 일일 보고서 보내기
* 쿼리 결과에 따라 알림 설정
* 클러스터에서 제어 명령 예약
* Azure 데이터 탐색기와 다른 데이터베이스 간에 데이터 내보내기 및 가져오기 

## <a name="limitations"></a>제한 사항

1. 클라이언트에 반환된 결과는 500,000개의 레코드로 제한됩니다. 이러한 레코드의 전체 메모리는 64MB 및 7분 실행 시간을 초과할 수 없습니다.
2. 현재 커넥터는 [포크](../query/forkoperator.md) 및 면 연산자()를 지원하지 않습니다. [facet](../query/facetoperator.md)
3. 흐름은 인터넷 익스플로러와 크롬에서 가장 잘 작동합니다.

##  <a name="login"></a>로그인 

1. [마이크로 소프트 흐름에](https://flow.microsoft.com/)로그인 .

1. Azure Kusto 커넥터에 처음으로 연결할 때 로그인하라는 메시지가 표시됩니다.

1. **로그인** 버튼을 클릭하고 자격 증명을 입력하여 Azure Kusto Flow를 사용하기 시작합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-signin.png "흐름 기호")

## <a name="authentication"></a>인증

Azure Kusto Flow 커넥터에 대한 인증은 사용자 자격 증명 또는 AAD 응용 프로그램을 사용하여 수행할 수 있습니다.



### <a name="aad-application-authentication"></a>AAD 응용 프로그램 인증

다음 단계를 사용하여 AAD 응용 프로그램을 사용하여 Azure Kusto Flow를 인증할 수 있습니다.

> 참고: 응용 프로그램이 [AAD 응용 프로그램이며](../management/access-control/how-to-provision-aad-app.md) 클러스터에서 쿼리를 실행할 권한이 있는지 확인합니다.

1. Azure Kusto 커넥터의 오른쪽 상단에 있는 세 개의 ![alt text](./Images/KustoTools-Flow/flow-addconnection.png "흐름 추가 연결") 점을 클릭합니다.

1. '새 연결 추가'를 선택한 다음 '서비스 주체와 연결'을 클릭합니다.
![대체 텍스트](./Images/KustoTools-Flow/flow-signin.png "흐름 기호")

1. 응용 프로그램 ID, 응용 프로그램 키 및 테넌트 ID를 입력합니다.

예를 들어 Microsoft 테넌트 ID는 72f988bf-86f1-41af-91ab-2d7cd011db47입니다.
연결 이름 값은 추가된 새 연결을 인식하기 위한 선택한 문자열입니다.
![대체 텍스트](./Images/KustoTools-Flow/flow-appauth.png "흐름 -appauth")

인증이 완료되면 흐름이 추가된 새 연결을 사용하고 있는지 확인할 수 있습니다.
![대체 텍스트](./Images/KustoTools-Flow/flow-appauthcomplete.png "흐름-가마우지 완료")

이제부터이 흐름은 응용 프로그램 자격 증명을 사용하여 실행됩니다.

## <a name="find-the-azure-kusto-connector"></a>Azure Kusto 커넥터 찾기

Azure Kusto 커넥터를 사용하려면 먼저 트리거를 추가해야 합니다. 트리거는 되풀이 기간 또는 이전 흐름 작업에 대한 응답으로 정의할 수 있습니다.

1. [새 흐름을 만듭니다.](https://flow.microsoft.com/manage/flows/new)
2. 첫 번째 단계로 '일정 - 되풀이'를 추가합니다.
3. 두 번째 단계의 검색 상자에 'Azure Kusto'를 입력합니다.

이제 아래 이미지에서 볼 수 있듯이 'Azure Kusto'를 볼 수 있습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-actions.png "흐름 작업")

## <a name="azure-kusto-flow-actions"></a>Azure 쿠스토 흐름 작업

흐름에서 Azure Kusto 커넥터를 검색할 때 흐름에 추가할 수 있는 3가지 가능한 작업이 표시됩니다.

다음 섹션에서는 각 Azure Kusto Flow 작업에 대한 기능 및 매개 변수에 대해 설명합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-actions.png "흐름 작업")

### <a name="azure-kusto---run-query-and-visualize-results"></a>Azure Kusto - 쿼리를 실행하고 결과를 시각화합니다.

> 참고: 쿼리가 점으로 시작하는 [경우(제어 명령의미)](../management/index.md)컨트롤 실행 명령을 사용하고 [결과를 시각화하십시오.](./flow.md#azure-kusto---run-control-command-and-visualize-results)

Kusto 쿼리 결과를 테이블 또는 차트로 시각화하려면 'Azure Kusto - 쿼리 실행 및 결과 시각화' 작업을 사용할 수 있습니다. 이 작업의 결과는 나중에 전자 메일을 통해 전송될 수 있습니다. 예를 들어 일별 ICM 보고서를 받으려는 경우 이 흐름을 사용할 수 있습니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-runquery.png "흐름 실행 쿼리")

이 예제에서는 쿼리 결과가 HTML 테이블로 반환됩니다.

### <a name="azure-kusto---run-control-command-and-visualize-results"></a>Azure Kusto - 제어 명령을 실행하고 결과를 시각화합니다.

'Azure Kusto - 쿼리 실행 및 결과 시각화' 작업과 마찬가지로 'Azure Kusto - 실행 컨트롤 명령 및 결과 시각화' 작업을 사용하여 [제어](../management/index.md) 명령을 실행할 수도 있습니다.
이 작업의 결과는 나중에 전자 메일을 통해 테이블또는 차트로 전송될 수 있습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-runcontrolcommand.png "흐름 실행 제어 명령")

이 예제에서는 컨트롤 명령의 결과가 원형 차트로 렌더링됩니다.

### <a name="azure-kusto---run-query-and-list-results"></a>Azure Kusto - 쿼리 실행 및 목록 결과

> 참고: 쿼리가 점으로 시작하는 [경우(제어 명령의미)](../management/index.md)컨트롤 실행 명령을 사용하고 [결과를 시각화하십시오.](./flow.md#azure-kusto---run-control-command-and-visualize-results)

이 작업은 Kusto 클러스터에 쿼리를 보냅니다. 이후에 추가되는 작업은 쿼리 결과의 각 줄을 반복합니다.

다음 예제는 1분마다 쿼리를 트리거하고 쿼리 결과를 기반으로 전자 메일을 보냅니다. 쿼리는 데이터베이스의 줄 수를 확인한 다음 줄 수가 0보다 큰 경우에만 전자 메일을 보냅니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-runquerylistresults.png "흐름 실행 쿼리 목록 결과")

열에 여러 줄이 있는 경우 열의 각 줄에 대해 다음 커넥터가 실행됩니다.

## <a name="email-kusto-query-results"></a>이메일 쿠스토 쿼리 결과

이메일 보고서를 보내려면 다음 단계를 수행합니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-sendemail.png "플로우-송메일")

1. '+ 새 단계'를 클릭한 다음 '액션 추가'를 클릭합니다.
2. 검색 상자에 'Office 365 Outlook - 전자 메일 보내기'를 입력합니다.
3. '받는 사람'을 이메일 주소로 설정하고 일부 텍스트에 '제목'을 설정하고 동적 콘텐츠에서 '본문' 필드에 'Body'를 추가합니다.
4. '고급 옵션'을 클릭하여 '첨부 파일 이름' 필드에 '첨부 파일 이름'을 추가하고 ' 첨부 파일 콘텐츠' 필드에 '첨부 파일 콘텐츠'를 추가하고 'HTML'이 '예'로 설정되어 있는지 확인합니다.
5. 상단 막대에서 이 흐름의 '흐름 이름'을 설정합니다.
6. '흐름 만들기'를 클릭하면 완료됩니다!

## <a name="how-to-make-sure-flow-succeeded"></a>흐름이 성공했는지 확인하는 방법은 무엇입니까?

마이크로 [소프트 플로우 홈 페이지로](https://flow.microsoft.com/)이동, [내 흐름을](https://flow.microsoft.com/manage/flows) 클릭한 다음 i 버튼을 클릭합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-myflows.png "플로우 마이플로우")

모든 흐름 실행은 상태, 시작 시간 및 기간으로 나열됩니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-runs.png "흐름 실행")

흐름의 마지막 실행을 열 때 흐름의 모든 단계가 녹색 V로 표시되면 흐름이 성공적으로 종료됩니다.
그렇지 않으면 빨간색 느낌표로 표시된 단계를 확장하여 오류 세부 정보를 봅니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-failed.png "흐름 실패")

일부 오류는 쿼리 구문 오류와 같은 직접 쉽게 해결할 수 있습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-syntaxerror.png "흐름 구문 오류")



## <a name="having-a-timeout-exception"></a>시간 시간 제외가 있습니까?

흐름이 실패하고 7분 이상 실행되는 경우 "RequestTimeout" 예외를 반환할 수 있습니다.

7분은 시간 초과 예외가 발생하기 전에 플로우 쿼리가 실행할 수 있는 최대 시간 입니다.

마이크로소프트 흐름 제한 을 보려면 [여기를 클릭하십시오.](#limitations)

시간이 제한되지 않고 변경할 수 있는 Kusto Explorer에서 동일한 쿼리가 성공적으로 실행될 수 있습니다.

"요청 시간 표시" 예외는 아래 이미지에 나와 있습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-requesttimeout.png "흐름 요청 시간 시간 시간")

문제를 해결하려면 다음 단계를 따를 수 있습니다.
1. [쿼리 모범 사례에](../query/best-practices.md)대해 자세히 알아보기.
2. 쿼리를 더 빠르게 실행하거나 청크로 구분하여 쿼리의 다른 부분에서 실행할 수 있도록 쿼리를 보다 효율적으로 만들어 보십시오.

## <a name="usage-examples"></a>사용 예제

이 섹션에는 Azure Kusto Flow 커넥터를 사용하는 몇 가지 일반적인 예제가 포함되어 있습니다.

### <a name="example-1---azure-kusto-flow-and-sql"></a>예제 1 - Azure 쿠스토 흐름 및 SQL

Azure Kusto 흐름을 사용하여 데이터를 쿼리한 다음 SQL DB에 누적할 수 있습니다. 
> 참고: SQL 삽입은 행당 별도로 수행되고 있으므로 적은 양의 출력 데이터에 대해서만 사용하십시오. 

![대체 텍스트](./Images/KustoTools-Flow/flow-sqlexample.png "흐름 sql예제")

### <a name="example-2---push-data-to-power-bi-dataset"></a>예제 2 - 데이터를 Power BI 데이터 집합으로 푸시

Azure Kusto Flow 커넥터를 Power BI 커넥터와 함께 사용하여 Kusto 쿼리에서 Power BI 스트리밍 데이터 집합으로 데이터를 푸시할 수 있습니다.

시작하려면 새 'Kusto - 쿼리 실행 및 결과 목록' 작업을 만듭니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-listresults.png "흐름 목록 결과")

'새 단계'를 클릭하고 '작업 추가'를 선택하고 'Power BI'를 검색합니다. '전원 BI - 데이터 집합에 행 추가'를 클릭합니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-powerbiconnector.png "유동 전력비 커넥터")

데이터를 푸시할 작업 영역, 데이터 집합 및 테이블을 입력합니다.
동적 콘텐츠 창에서 데이터 집합 스키마와 관련 Kusto 쿼리 결과가 포함된 페이로드를 추가합니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-powerbifields.png "플로우 파워비필드")

Flow는 Kusto 쿼리 결과 테이블의 각 행에 대해 Power BI 작업을 자동으로 적용합니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-powerbiforeach.png "플로우 파워비포리치")

### <a name="example-3---conditional-queries"></a>예 3 - 조건부 쿼리

Kusto 쿼리의 결과는 다음 흐름 작업에 대한 입력 또는 조건으로 사용할 수 있습니다.

다음 예제에서는 마지막 날에 발생한 인시던트에 대해 Kusto에 쿼리합니다. 해결된 각 인시던트에 대해 슬랙 메시지를 게시하고 푸시 알림을 만듭니다.
여전히 활성 상태인 각 인시던트에 대해 Kusto에서 유사한 인시던트에 대한 자세한 정보를 쿼리하고 해당 정보를 전자 메일로 보내고 관련 TFS 작업을 엽니다.

이 지침에 따라 유사한 흐름을 만듭니다.

새로운 'Kusto - 쿼리 실행 및 결과 목록' 작업을 만듭니다.
'새 단계'를 클릭하고 '조건 추가'를 선택합니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-listresults.png "흐름 목록 결과")

동적 콘텐츠 창에서 다음 작업의 조건으로 사용할 매개 변수를 선택합니다.
지정된 매개 변수에 특정 조건을 설정하려면 관계 및 값 유형을 선택합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-condition.png "흐름 조건")

Flow는 쿼리 결과 테이블의 각 행에 이 조건을 적용합니다.

조건이 true이고 false인 경우에 대한 작업을 추가합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-conditionactions.png "흐름 조건")

동적 콘텐츠 창에서 선택하여 Kusto 쿼리의 결과 값을 다음 작업에 대한 입력으로 사용할 수 있습니다.
아래에서 '슬랙 - 포스트 메시지' 액션과 '비주얼 스튜디오 - 새로운 작업 항목 만들기' 작업을 Kusto 쿼리의 데이터를 추가했습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-slack.png "흐름 여유")

![대체 텍스트](./Images/KustoTools-Flow/flow-visualstudio.png "플로우 비주얼 스튜디오")

이 예에서는 인시던트가 여전히 활성 상태인 경우 Kusto를 다시 쿼리하여 동일한 소스의 인시던트가 과거에 해결된 방법에 대한 정보를 얻습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-conditionquery.png "흐름 조건 쿼리")

이 정보를 원형 차트로 시각화하고 팀에 이메일로 보겠습니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-conditionemail.png "흐름 상태 이메일")

### <a name="example-4---email-multiple-azure-kusto-flow-charts"></a>예 4 - 여러 Azure Kusto 흐름 차트로 전자 메일

"되풀이" 트리거를 사용하여 새 흐름을 만들고 흐름과 빈도의 간격을 정의합니다. 

하나 이상의 'Kusto - 쿼리를 실행하고 결과를 시각화' 작업을 사용하여 새 단계를 추가합니다. 

![대체 텍스트](./Images/KustoTools-Flow/flow-severalqueries.png "흐름 -여러쿼리")

각 'Kusto - 쿼리 실행 및 결과 시각화'에 대해 다음 필드를 정의합니다.

클러스터 이름, 데이터베이스 이름, 쿼리 및 차트 유형(Html 테이블/원형 차트/시간 차트/가로 막대형 차트/ 사용자 지정 값 입력).

![대체 텍스트](./Images/KustoTools-Flow/flow-visualizeresultsmultipleattachments.png "흐름 시각화결과여러 첨부 파일")

'Kusto - 쿼리를 실행하고 결과를 시각화' 작업으로 완료한 후 '이메일 보내기' 작업을 추가합니다. 

쿼리의 시각화 결과를 전자 메일 본문에 연결하려면 필요한 본문을 "Body" 필드에 삽입해야 합니다.
또한 전자 메일에 첨부 파일을 추가하려면 '첨부 파일 이름'과 '첨부 파일 콘텐츠'를 추가합니다.

"HTML"에서 "예"를 선택해야 합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-emailmultipleattachments.png "흐름 전자 메일 다중 첨부 파일")

결과:

![대체 텍스트](./Images/KustoTools-Flow/flow-resultsmultipleattachments.png "흐름 결과다중 첨부 파일")

![대체 텍스트](./Images/KustoTools-Flow/flow-resultsmultipleattachments2.png "흐름 결과다중 첨부 파일2")

### <a name="example-5---send-a-different-email-to-different-contacts"></a>예 5 - 다른 연락처에 다른 이메일 보내기

Azure Kusto Flow를 활용하여 다른 연락처에 다른 사용자 지정 전자 메일을 보낼 수 있습니다. 이메일 주소와 이메일 내용은 Kusto 쿼리의 결과입니다.

아래 예제를 참조하십시오.

![대체 텍스트](./Images/KustoTools-Flow/flow-dynamicemailkusto.png "흐름 동적이메일쿠스토")

![대체 텍스트](./Images/KustoTools-Flow/flow-dynamicemail.png "흐름 동적 이메일")

### <a name="example-6---create-custom-html-table"></a>예제 6 - 사용자 지정 HTML 테이블 만들기

Azure Kusto Flow를 활용하여 사용자 지정 HTML 테이블과 같은 사용자 지정 HTML 요소를 만들고 사용할 수 있습니다.

다음 예제에서는 사용자 지정 HTML 테이블을 만드는 방법을 보여 줍니다. HTML 테이블에는 로그 수준(Kusto Explorer와 동일)으로 행이 색칠됩니다.

다음 지침을 따라 유사한 흐름을 만듭니다.

새로운 'Kusto - 쿼리 실행 및 결과 목록' 작업을 만듭니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-listresultforhtmltable.png "흐름 목록결과")

쿼리 결과를 반복하고 HTML 테이블 본문을 만듭니다. 먼저 HTML 문자열을 보유하는 변수를 만듭니다 - '새 단계'를 클릭하고 '작업 추가'를 선택하고 '변수'를 검색합니다. '변수 - 변수 초기화'를 클릭합니다. 다음과 같이 문자열 변수를 초기화합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-initializevariable.png "흐름 초기화 변수")

결과 반복 - '새 단계'를 클릭하고 '작업 추가'를 선택합니다. '변수'를 검색합니다. '변수 - 문자열 변수에 부속'을 선택합니다. 이전에 초기화한 변수 이름을 선택하고 쿼리 결과를 사용하여 HTML 테이블 행을 만듭니다. 쿼리 결과를 선택할 때 '각 에 적용'은 자동 매칭이 추가됩니다.

아래 예제에서는 다음 경우 식을 사용하여 각 행의 스타일을 정의합니다.

if(항목('Apply_to_each')[' 수준'], '경고'), '노란색', if(항목('Apply_to_each')?[' 수준'), '오류'), '빨간색', '흰색'))

![대체 텍스트](./Images/KustoTools-Flow/flow-createhtmltableloopcontent.png "흐름 - 생성htmltable루프콘텐츠")

마지막으로 전체 HTML 콘텐츠를 만듭니다. '각 작업에 적용'외부에 새 작업을 추가합니다. 다음 예제에서 사용되는 작업은 '이메일 보내기'입니다. 이전 단계의 변수를 사용하여 HTML 테이블을 정의합니다. 이메일을 보내는 경우 '고급 옵션 표시'를 클릭하고 'HTML'에서 '예'를 선택합니다.

![대체 텍스트](./Images/KustoTools-Flow/flow-customhtmltablemail.png "흐름 사용자 정의 htmltablemail")

그리고 여기에 결과입니다 :

![대체 텍스트](./Images/KustoTools-Flow/flow-customhtmltableresult.png "흐름-사용자 지정 가능 결과")






