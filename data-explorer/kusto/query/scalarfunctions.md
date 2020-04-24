---
title: 스칼라 기능 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 스칼라 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: ef807c93f9ae9b125439f55f32a2bb1eb21d46b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509557"
---
# <a name="scalar-functions"></a>스칼라 함수

## <a name="binary-functions"></a>바이너리 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|비트 및 두 값 간의 작업 결과를 반환합니다.|
|[binary_not()](binary-notfunction.md)|입력 값의 비트 부정을 반환합니다.|
|[binary_or()](binary-orfunction.md)|두 값의 비트 또는 연산을 반환합니다.|
|[binary_shift_left()](binary-shift-leftfunction.md)| << n: 숫자 쌍에 이진 시프트 왼쪽 작업을 반환합니다.|
|[binary_shift_right()](binary-shift-rightfunction.md)|한 쌍의 숫자에서 이진 시프트 오른쪽 작업을 반환합니다: >> n.|
|[binary_xor()](binary-xorfunction.md)|두 값의 비트별 xor 연산 결과를 반환합니다.|
|[bitset_count_ones()](bitset-count-onesfunction.md)|숫자의 이진 표현에서 세트 비트 수를 반환합니다.|

## <a name="conversion-functions"></a>변환 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|입력을 부울(서명된 8비트) 표현으로 변환합니다.|
|[todatetime()](todatetimefunction.md)|입력을 날짜 시간 스칼라로 변환합니다.|
|[토더블()/토리얼()](todoublefunction.md)|입력을 실제 형식 값으로 변환합니다. (todouble() 및 토리얼()은 동의어입니다.|
|[토스트링()](tostringfunction.md)|입력을 문자열 표현으로 변환합니다.|
|[시간 범위()](totimespanfunction.md)|입력을 시간 범위 스칼라로 변환합니다.|


## <a name="datetimetimespan-functions"></a>날짜 시간/시간 범위 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|현재 UTC 시계 시간에서 지정된 시간 범위를 뺍니다.|
|[datetime_add()](datetime-addfunction.md)|지정된 날짜에서 새 날짜 시간을 계산합니다part 지정된 날짜에 추가된 지정된 금액을 곱합니다.|
|[datetime_part()](datetime-partfunction.md)|요청된 날짜 파트를 정수 값으로 추출합니다.|
|[datetime_diff()](datetime-difffunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 연도의 끝을 반환합니다.|
|[데이오브월()](dayofmonthfunction.md)|지정된 월의 일 수를 나타내는 정수 번호를 반환합니다.|
|[주간()](dayofweekfunction.md)|이전 일요일 이후의 정수 수를 시간 범위로 반환합니다.|
|[일보()](dayofyearfunction.md)|지정된 연도의 일 수를 나타내는 정수 번호를 반환합니다.|
|[endofday()](endofdayfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 날짜의 끝을 반환합니다.|
|[endofmonth()](endofmonthfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 월의 끝을 반환합니다.|
|[endofweek()](endofweekfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 주말을 반환합니다.|
|[()](endofyearfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 연도의 끝을 반환합니다.|
|[format_datetime()](format-datetimefunction.md)|형식 패턴 매개 변수를 기반으로 datetime 매개 변수의 서식을 지정합니다.|
|[format_timespan()](format-timespanfunction.md)|형식 패턴 매개 변수를 기반으로 형식 시간 범위 매개 변수의 서식을 지정합니다.|
|[getmonth()](getmonthfunction.md)|datetime에서 월 번호(1-12)를 가져옵니다.|
|[getyear()](getyearfunction.md)|날짜 시간 인수의 연도 부분을 반환합니다.|
|[시간()](hourofdayfunction.md)|지정된 날짜의 시간 번호를 나타내는 정수 번호를 반환합니다.|
|[make_datetime()](make-datetimefunction.md)|지정된 날짜 및 시간에서 날짜 시간 스칼라 값을 만듭니다.|
|[make_timespan()](make-timespanfunction.md)|지정된 기간에서 시간 범위 스칼라 값을 만듭니다.|
|[()](monthofyearfunction.md)|지정된 연도의 월 수를 나타내는 정수 번호를 반환합니다.|
|[지금()](nowfunction.md)|지정된 시간 범위로 선택적으로 오프셋된 현재 UTC 클럭 시간을 반환합니다.|
|[시작일()](startofdayfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 날짜의 시작을 반환합니다.|
|[시작월()](startofmonthfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 월의 시작을 반환합니다.|
|[시작 주간()](startofweekfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 주의 시작을 반환합니다.|
|[스타트오브이어()](startofyearfunction.md)|제공된 경우 오프셋으로 이동된 날짜를 포함하는 연도의 시작을 반환합니다.|
|[todatetime()](todatetimefunction.md)|입력을 날짜 시간 스칼라로 변환합니다.|
|[시간 범위()](totimespanfunction.md)|입력을 시간 범위 스칼라로 변환합니다.|
|[주간 연도()](weekofyearfunction.md)|주 번호를 나타내는 정수를 반환합니다.|


## <a name="dynamicarray-functions"></a>동적/배열 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|여러 동적 배열을 단일 배열에 연결합니다.|
|[array_iif()](arrayifffunction.md)|배열에 요소 별 iif 함수를 적용합니다.|
|[array_index_of()](arrayindexoffunction.md)|지정된 항목에 대한 배열을 검색하고 해당 위치를 반환합니다.|
|[array_length()](arraylengthfunction.md)|동적 배열의 요소 수를 계산합니다.|
|[array_slice()](arrayslicefunction.md)|동적 배열의 조각을 추출합니다.|
|[array_split()](arraysplitfunction.md)|입력 배열에서 분할된 배열배열을 빌드합니다.|
|[bag_keys()](bagkeysfunction.md)|동적 속성-bag 개체의 모든 루트 키를 배분합니다.|
|[팩()](packfunction.md)|이름과 값 목록에서 동적 개체(속성 가방)를 만듭니다.|
|[pack_all()](packallfunction.md)|테이블 형식식의 모든 열에서 동적 개체(속성 가방)를 만듭니다.|
|[pack_array()](packarrayfunction.md)|모든 입력 값을 동적 배열로 압축합니다.|
|[반복()](repeatfunction.md)|일련의 동일한 값을 보유 하는 동적 배열을 생성 합니다.|
|[set_difference()](setdifferencefunction.md)|첫 번째 배열에 있지만 다른 배열에는 없는 모든 고유 값 집합의 배열을 반환합니다.|
|[set_has_element()](sethaselementfunction.md)|지정된 배열에 지정된 요소가 포함되어 있는지 여부를 확인합니다.|
|[set_intersect()](setintersectfunction.md)|모든 배열에 있는 모든 고유 값 집합의 배열을 반환합니다.|
|[set_union()](setunionfunction.md)|제공된 배열에 있는 모든 고유 값 집합의 배열을 반환합니다.|
|[트리 경로()](treepathfunction.md)|동적 개체의 리프를 식별하는 모든 path 식을 열거합니다.|
|[지퍼()](zipfunction.md)|zip 함수는 임의의 수의 동적 배열을 허용하고, 해당 요소는 각각 동일한 인덱스의 입력 배열의 요소를 포함하는 배열인 배열을 반환합니다.|


## <a name="window-scalar-functions"></a>윈도우 스칼라 기능

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[다음()](nextfunction.md)|직렬화된 행 집합의 경우 오프셋에 따라 이후 행에서 지정된 열값을 반환합니다.|
|[이전()](prevfunction.md)|직렬화된 행 집합의 경우 오프셋에 따라 이전 행에서 지정된 열값을 반환합니다.|
|[row_cumsum()](rowcumsumfunction.md)|열의 누적 합계를 계산합니다.|
|[row_number()](rownumberfunction.md)|직렬화된 행 집합에서 행 번호를 반환합니다.|

## <a name="flow-control-functions"></a>흐름 제어 기능

|함수 이름            |Description                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|평가된 식의 스칼라 상수 값을 반환합니다.|

## <a name="mathematical-functions"></a>수학 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[복근()](abs-function.md)|입력의 절대값을 계산합니다.|
|[아코스()](acosfunction.md)|코신이 지정된 숫자(cos()의 역작업)인 각도를 반환합니다.|
|[아신()](asinfunction.md)|죄가 지정된 숫자(죄()의 역작업)인 각도를 반환합니다.|
|[아탄()](atanfunction.md)|접선이 지정된 숫자인 각도를 반환합니다(tan()의 역 작업).|
|[아탄2()](atan2function.md)|양수 x축과 원점에서 점(y, x)까지의 광선 사이의 각도(라디안)를 계산합니다.|
|[beta_cdf()](beta-cdffunction.md)|표준 누적 베타 배포 함수를 반환합니다.|
|[beta_inv()](beta-invfunction.md)|베타 누적 확률 베타 밀도 함수의 역버전을 반환합니다.|
|[beta_pdf()](beta-pdffunction.md)|확률 밀도 베타 함수를 반환합니다.|
|[코스()](cosfunction.md)|코사네 함수를 반환합니다.|
|[요정()](cotfunction.md)|라디안에서 지정된 각도의 삼각선 코탄젠트를 계산합니다.|
|[도()](degreesfunction.md)|수식 도 = (180 / PI) * 각도 인 라디안을 사용하여 라디안의 각도 값을 값으로 변환합니다.|
|[()](exp-function.md)|x의 기본 e 지수 함수는 e가 전원 x: e^x로 상승합니다.|
|[exp10()](exp10-function.md)|x의 기본-10 지수 함수는 10이 힘 x: 10^x로 상승합니다.|
|[exp2()](exp2-function.md)|2의 기본-2 지수 함수인 x는 2로 상승하여 x: 2^x로 상승합니다.|
|[감마()](gammafunction.md)|감마 함수를 계산합니다.|
|[해시()](hashfunction.md)|입력 값에 대한 해시 값을 반환합니다.|
|[hash_combine()](hash_combinefunction.md)|두 개 이상의 해시 값을 결합합니다.|
|[hash_many()](hash_manyfunction.md)|여러 값의 결합된 해시 값을 반환합니다.|
|[isfinite()](isfinitefunction.md)|입력이 유한 값인지 여부를 반환합니다(무한또는 NaN도 아닙니다).|
|[isinf()](isinffunction.md)|입력이 무한(양수 또는 음수) 값인지 여부를 반환합니다.|
|[isnan()](isnanfunction.md)|입력이 NaN(숫자가 아님) 값인지 여부를 반환합니다.|
|[로그()](log-function.md)|자연 로그릿 흐 기능을 반환합니다.|
|[log10()](log10-function.md)|코몬(base-10) 로그릿섬 기능을 리툰합니다.|
|[log2()](log2-function.md)|기본-2 로그릿hm 함수를 반환합니다.|
|[로그감마()](loggammafunction.md)|감마 함수의 절대 값로그를 계산합니다.|
|[(하지 않음)](notfunction.md)|bool 인수의 값을 반대로 합니다.|
|[파이()](pifunction.md)|Pi(π)의 상수 값을 반환합니다.|
|[포우()](powfunction.md)|전원을 높인 결과를 반환합니다.|
|[라디안()](radiansfunction.md)|수식 라디안을 사용하여 각도 값을 각도 값을 radians = (PI / 180) * 각도 각도를 사용하여 각도 값을 radians의 값으로 변환합니다.|
|[랜드()](randfunction.md)|난수를 반환합니다.|
|[범위()](rangefunction.md)|일련의 균등 한 간격 값을 보유 하는 동적 배열을 생성 합니다.|
|[라운드()](roundfunction.md)|반올림된 소스를 지정된 정밀도로 반환합니다.|
|[기호()](signfunction.md)|숫자 식의 부호입니다.|
|[죄()](sinfunction.md)|사위 함수를 반환합니다.|
|[sqrt()](sqrtfunction.md)|제곱근 함수를 반환합니다.|
|[황갈색()](tanfunction.md)|접선 함수를 반환합니다.|
|[welch_test()](welch-testfunction.md)|[웰치 테스트 함수의](https://en.wikipedia.org/wiki/Welch%27s_t-test)p 값을 계산합니다.|


## <a name="metadata-functions"></a>메타데이터 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|열 이름을 문자열과 기본값으로 지정합니다. 그렇지 않으면 열이 있는 경우 열에 대한 참조를 반환합니다.|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|쿼리를 실행하는 현재 클러스터를 반환합니다.|
|[current_database()](current-database-function.md)|범위에서 데이터베이스 이름을 반환합니다.|
|[current_principal()](current-principalfunction.md)|이 쿼리를 실행하는 현재 보안 주체를 반환합니다.|
|[current_principal_details()](current-principal-detailsfunction.md)|쿼리를 실행하는 보안 주체에 대한 세부 정보를 반환합니다.|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|쿼리를 실행하는 현재 보안 주체의 그룹 구성원 자격 또는 보안 주체 ID를 확인합니다.|
|[cursor_after()](cursorafterfunction.md)|커서의 이전 값 이후에 인젝스된 레코드에 액세스하는 데 사용됩니다.|
|[estimate_data_size()](estimate-data-sizefunction.md)|테이블 형식 식의 선택한 열의 예상 데이터 크기를 반환합니다.|
|[extent_id()](extentidfunction.md)|현재 레코드에 있는 데이터 샤드("익스텐츠")를 식별하는 고유 식별자를 반환합니다.|
|[extent_tags()](extenttagsfunction.md)|현재 레코드가 있는 데이터 샤드("익스텐츠")의 태그가 있는 동적 배열을 반환합니다.|
|[ingestion_time()](ingestiontimefunction.md)|레코드의 숨겨진 날짜 시간 열 또는 null을 $IngestionTime 검색합니다.|


## <a name="rounding-functions"></a>반올림 기능

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[빈()](binfunction.md)|값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다.|
|[bin_at()](binatfunction.md)|Bin의 시작점을 제어하여 고정 크기 "bin"으로 값을 반올림합니다. (또한 bin 함수를 참조하십시오.)|
|[천장()](ceilingfunction.md)|지정된 숫자 식보다 크거나 같을 가장 작은 정수를 계산합니다.|
|[바닥()](floorfunction.md)|값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다.|


## <a name="conditional-functions"></a>조건부 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[케이스()](casefunction.md)|조건자 목록을 평가하고 조건어가 충족되는 첫 번째 결과 식을 반환합니다.|
|[유합()](coalescefunction.md)|식 목록을 평가하고 첫 번째 비null(또는 문자열에 대해 비어 없는) 식을 반환합니다.|
|[iif()/iff()](iiffunction.md)|첫 번째 인수(술어)를 평가하고 술어가 true(두 번째) 또는 false(세 번째)로 평가되는지 여부에 따라 두 번째 또는 세 번째 인수의 값을 반환합니다.|
|[max_of()](max-offunction.md)|평가된 여러 숫자 식의 최대값을 반환합니다.|
|[min_of()](min-offunction.md)|평가된 여러 숫자 식의 최소값을 반환합니다.|

## <a name="series-element-wise-functions"></a>시리즈 요소 별 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|두 개의 숫자 계열 입력의 요소별 추가를 계산합니다.|
|[series_divide()](series-dividefunction.md)|두 개의 숫자 계열 입력의 요소별 분할을 계산합니다.|
|[series_equals()](series-equalsfunction.md)|두 개의 숫자 계열`==`입력의 요소 별 () 논리 연산을 계산합니다.|
|[series_greater()](series-greaterfunction.md)|두 개의 숫자 계열`>`입력의 요소 별 큰 () 논리 연산을 계산합니다.|
|[series_greater_equals()](series-greater-equalsfunction.md)|두 개의 숫자 계열 입력의`>=`요소 별 크거나 같음 () 논리 연산을 계산합니다.|
|[series_less()](series-lessfunction.md)|두 개의 숫자 계열`<`입력의 요소 별 () 논리 연산을 계산합니다.|
|[series_less_equals()](series-less-equalsfunction.md)|두 개의 숫자 계열 입력의 요소 별 이하 또는 같음 ()`<=`논리 연산을 계산합니다.|
|[series_multiply()](series-multiplyfunction.md)|두 개의 숫자 계열 입력의 요소별 곱셈을 계산합니다.|
|[series_not_equals()](series-not-equalsfunction.md)|두 개의 숫자 계열 입력의 요소 별 ()`!=`논리 연산을 계산합니다.|
|[series_subtract()](series-subtractfunction.md)|두 개의 숫자 계열 입력의 요소별 빼기 계산합니다.|

## <a name="series-processing-functions"></a>시리즈 프로세싱 기능

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|계열을 구성요소로 분해합니다.|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|계열 분해를 기반으로 한 계열에서 이상 징후를 찾습니다.|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|계열 분해를 기반으로 예측합니다.|
|[series_fill_backward()](series-fill-backwardfunction.md)|계열에서 누락된 값의 뒤로 채우기 보간을 수행합니다.|
|[series_fill_const()](series-fill-constfunction.md)|계열의 누락된 값을 지정된 상수 값으로 바꿉습니다.|
|[series_fill_forward()](series-fill-forwardfunction.md)|계열에서 누락된 값의 정방향 채우기 보간을 수행합니다.|
|[series_fill_linear()](series-fill-linearfunction.md)|계열에서 누락된 값의 선형 보간을 수행합니다.|
|[series_fir()](series-firfunction.md)|계열에 유한 임펄스 응답 필터를 적용합니다.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|계열에 두 개의 세그먼트 선형 회귀를 적용하여 여러 열을 반환합니다.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|동적 객체를 반환하는 계열에 두 세그먼트 선형 회귀를 적용합니다.|
|[series_fit_line()](series-fit-linefunction.md)|계열에 선형 회귀를 적용하여 여러 열을 반환합니다.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|동적 오브젝트를 반환하는 계열에 선형 회귀를 적용합니다.|
|[series_iir()](series-iirfunction.md)|계열에 무한 임펄스 응답 필터를 적용합니다.|
|[series_outliers()](series-outliersfunction.md)|시리즈의 이상 점수를 점수로 합니다.|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|두 계열의 Pearson 상관 계수를 계산합니다.|
|[series_periods_detect()](series-periods-detectfunction.md)|타임시리즈에 존재하는 가장 중요한 기간을 찾습니다.|
|[series_periods_validate()](series-periods-validatefunction.md)|타임시리즈에 지정된 길이의 주기적 패턴이 포함되어 있는지 확인합니다.|
|[series_seasonal()](series-seasonalfunction.md)|계열의 계절 구성 요소를 찾습니다.|
|[series_stats()](series-statsfunction.md)|여러 열의 계열에 대한 통계를 반환합니다.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|동적 개체의 계열에 대한 통계를 반환합니다.|

## <a name="string-functions"></a>문자열 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|문자열을 base64 문자열로 인코딩합니다.|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|BASE64 문자열을 UTF-8 문자열로 디코딩합니다.|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|base64 문자열을 긴 값의 배열로 디코딩합니다.|
|[countof()](cotfunction.md)|문자열의 부분 문자열 발생을 계산합니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.|
|[추출()](extractfunction.md)|텍스트 문자열에서 정규식 에 대한 일치 항목을 가져옵니다.|
|[extract_all()](extractallfunction.md)|텍스트 문자열에서 정규식에 대한 모든 일치 항목을 가져옵니다.|
|[[1]](extractjsonfunction.md)|path 식을 사용하여 JSON 텍스트에서 지정된 요소를 가져옵니다.|
|[인덱도()](indexoffunction.md)|함수는 입력 문자열 내에서 지정된 문자열이 처음 발생하는 0기반 인덱스를 보고합니다.|
|[[공백]()](isemptyfunction.md)|인수가 빈 문자열이거나 null인 경우 true를 반환합니다.|
|[isnotempty()](isnotemptyfunction.md)|인수가 빈 문자열이 아니거나 null인 경우 true를 반환합니다.|
|[이스노널()](isnotnullfunction.md)|인수가 null이 아닌 경우 true를 반환합니다.|
|[이스널()](isnullfunction.md)|유일한 인수를 평가하고 인수가 null 값으로 평가되는지 여부를 나타내는 bool 값을 반환합니다.|
|[parse_csv()](parsecsvfunction.md)|쉼표 분리 된 값을 나타내는 주어진 된 문자열을 분할 하 고 이러한 값으로 문자열 배열을 반환 합니다.|
|[parse_ipv4()](parse-ipv4function.md)|입력을 긴(서명된 64비트) 숫자 표현으로 변환합니다.|
|[parse_json()](parsejsonfunction.md)|문자열을 JSON 값으로 해석하고 값을 동적값으로 반환합니다.|
|[parse_url()](parseurlfunction.md)|절대 URL 문자열을 구문 분석하 고 동적 개체URL의 모든 부분을 포함 하는 반환 합니다.|
|[parse_urlquery()](parseurlqueryfunction.md)|URL 쿼리 문자열을 구문 분석하 고 동적 개체에 쿼리 매개 변수가 포함 된 반환 합니다.|
|[parse_version()](parse-versionfunction.md)|버전의 입력 문자열 표현을 비슷한 소수 자릿수로 변환합니다.|
|[바꾸기()](replacefunction.md)|모든 정규식 일치 항목을 다른 문자열로 바꿉니다.|
|[역()](reversefunction.md)|함수는 입력 문자열을 반대로 만듭니다.|
|[분할()](splitfunction.md)|지정된 구분 기호에 따라 지정된 문자열을 분할하고 포함된 하위 문자열이 있는 문자열 배열을 반환합니다.|
|[스트캣()](strcatfunction.md)|1인수와 64개의 인수를 연결합니다.|
|[strcat_delim()](strcat-delimfunction.md)|첫 번째 인수로 제공되는 구분 기호와 함께 2개에서 64개 인수 사이에 연결합니다.|
|[strcmp()](strcmpfunction.md)|두 문자열을 비교합니다.|
|[스트렌()](strlenfunction.md)|입력 문자열의 길이(문자)를 반환합니다.|
|[strrep()](strrepfunction.md)|주어진 문자열이 제공된 시간(기본값 - 1)을 반복합니다.|
|[서브스트링()](substringfunction.md)|일부 인덱스에서 문자열끝까지 시작하는 소스 문자열에서 하위 문자열을 추출합니다.|
|[토퍼()](toupperfunction.md)|문자열을 대문자로 변환합니다.|
|[번역()](translatefunction.md)|지정된 문자열에서 문자 집합('searchList')을 다른 문자 집합('replacementList')으로 바꿉니다.|
|[트림()](trimfunction.md)|지정된 정규식의 모든 선행 및 후행 일치 항목을 제거합니다.|
|[trim_end()](trimendfunction.md)|지정된 정규식의 후행 일치를 제거합니다.|
|[trim_start()](trimstartfunction.md)|지정된 정규식의 선행 일치를 제거합니다.|
|[url_decode()](urldecodefunction.md)|이 함수는 인코딩된 URL을 일반 URL 표현으로 변환합니다.|
|[url_encode()](urlencodefunction.md)|이 함수는 입력 URL의 문자를 인터넷을 통해 전송할 수 있는 형식으로 변환합니다.|

## <a name="ip-v4-functions"></a>IP v4 기능

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|두 개의 IPv4 문자열을 비교합니다.|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|두 개의 IPv4 문자열을 일치시다.|
|[parse_ipv4()](parse-ipv4function.md)|입력 문자열을 긴(서명된 64비트) 숫자 표현으로 변환합니다.|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|입력 문자열 및 IP 접두사 마스크를 긴(서명된 64비트) 숫자 표현으로 변환합니다.|

## <a name="type-functions"></a>형식 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|단일 인수의 런타임 형식을 반환합니다.|


## <a name="scalar-aggregation-functions"></a>스칼라 집계 함수

|함수 이름     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|hll 결과(hll 또는 hll 병합에 의해 생성된)에서 dcount를 계산합니다.|
|[hll_merge()](hllmergefunction.md)|hll 결과 병합 (집계 버전 hll 병합()의 스칼라 버전).|
|[percentile_tdigest()](percentile-tdigestfunction.md)|tdigest 결과(tdigest 또는 병합-tdigests에 의해 생성된) 결과에서 백분위수 결과를 계산합니다.|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|데이터 집합에서 값의 백분율 순위를 계산합니다.|
|[rank_tdigest()](rank-tdigest.md)|집합에서 값의 상대 순위를 계산합니다.|
|[tdigest_merge()](tdigest-mergefunction.md)|tdigest 결과 병합 (집계 버전 tdigest-merge()의 스칼라 버전)|

## <a name="geo-spatial-functions"></a>지리 공간 기능

|함수 이름|Description|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|지구상의 두 지리 공간 좌표 사이의 가장 짧은 거리를 계산합니다.|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|지오해시 직사각형 영역의 중심을 나타내는 지리 공간 좌표를 계산합니다.|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|지리 공간 좌표가 지구의 원 안에 있는지 여부를 계산합니다.|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|지리적 위치에 대한 Geohash 문자열 값을 계산합니다.|