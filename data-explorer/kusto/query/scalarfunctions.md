---
title: 스칼라 함수-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 스칼라 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 4e16e739048bc12a0a621e7fd98933b8e60073be
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91765441"
---
# <a name="scalar-function-types"></a>스칼라 함수 유형

## <a name="binary-functions"></a>이진 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|두 값 사이의 비트 and 연산의 결과를 반환 합니다.|
|[binary_not()](binary-notfunction.md)|입력 값의 비트 부정을 반환 합니다.|
|[binary_or()](binary-orfunction.md)|두 값의 비트 or 연산 결과를 반환 합니다.|
|[binary_shift_left()](binary-shift-leftfunction.md)| << n의 숫자 쌍에 대해 이항 시프트 왼쪽 연산을 반환 합니다.|
|[binary_shift_right()](binary-shift-rightfunction.md)| >> n의 숫자 쌍에 대해 이항 시프트 오른쪽 연산을 반환 합니다.|
|[binary_xor()](binary-xorfunction.md)|두 값의 비트 xor 연산의 결과를 반환 합니다.|
|[bitset_count_ones()](bitset-count-onesfunction.md)|숫자의 이진 표현에서 설정 비트 수를 반환 합니다.|

## <a name="conversion-functions"></a>변환 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|입력을 부울 (부호 있는 8 비트) 표현으로 변환 합니다.|
|[todatetime()](todatetimefunction.md)|입력을 datetime 스칼라로 변환 합니다.|
|[todouble ()/toreal ()](todoublefunction.md)|입력을 real 형식의 값으로 변환 합니다. (todouble () 및 toreal ()는 동의어입니다.)|
|[tostring ()](tostringfunction.md)|입력을 문자열 표현으로 변환 합니다.|
|[totimespan()](totimespanfunction.md)|입력을 timespan 스칼라로 변환 합니다.|

## <a name="datetimetimespan-functions"></a>DateTime/timespan 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|현재 UTC 시계 시간에서 지정된 시간 범위를 뺍니다.|
|[datetime_add ()](datetime-addfunction.md)|지정 된 날짜/시간에 지정한 값을 곱하여 지정 된 날짜/시간에 추가 된 새 날짜/시간을 계산 합니다.|
|[datetime_part ()](datetime-partfunction.md)|요청 된 날짜 부분을 정수 값으로 추출 합니다.|
|[datetime_diff ()](datetime-difffunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 연도의 끝을 반환 합니다.|
|[dayofmonth()](dayofmonthfunction.md)|지정 된 월의 일 수를 나타내는 정수를 반환 합니다.|
|[dayofweek()](dayofweekfunction.md)|이전 일요일 이후의 날짜 수를 timespan으로 반환 합니다.|
|[dayofyear()](dayofyearfunction.md)|지정 된 연도의 일 수를 나타내는 정수를 반환 합니다.|
|[endofday()](endofdayfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 일의 끝을 반환 합니다.|
|[endofmonth()](endofmonthfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 월의 끝을 반환 합니다.|
|[endofweek()](endofweekfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 주의 끝을 반환 합니다.|
|[endofyear()](endofyearfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 연도의 끝을 반환 합니다.|
|[format_datetime()](format-datetimefunction.md)|형식 패턴 매개 변수를 기반으로 datetime 매개 변수의 형식을 지정 합니다.|
|[format_timespan()](format-timespanfunction.md)|형식 패턴 매개 변수를 기반으로 형식 timespan 매개 변수의 형식을 지정 합니다.|
|[getmonth()](getmonthfunction.md)|datetime에서 월 번호(1-12)를 가져옵니다.|
|[getyear()](getyearfunction.md)|Datetime 인수의 연도 부분을 반환 합니다.|
|[hourofday()](hourofdayfunction.md)|지정 된 날짜의 시간을 나타내는 정수 값을 반환 합니다.|
|[make_datetime()](make-datetimefunction.md)|지정 된 날짜 및 시간에서 datetime 스칼라 값을 만듭니다.|
|[make_timespan()](make-timespanfunction.md)|지정 된 기간에서 timespan 스칼라 값을 만듭니다.|
|[monthofyear()](monthofyearfunction.md)|지정 된 연도의 월 수를 나타내는 정수를 반환 합니다.|
|[now()](nowfunction.md)|지정 된 시간 범위에 따라 선택적으로 오프셋 되는 현재 UTC 시계 시간을 반환 합니다.|
|[startofday()](startofdayfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 일의 시작을 반환 합니다.|
|[startofmonth()](startofmonthfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 월의 시작을 반환 합니다.|
|[startofweek()](startofweekfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 주의 시작을 반환 합니다.|
|[startofyear()](startofyearfunction.md)|지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 연도의 시작을 반환 합니다.|
|[todatetime()](todatetimefunction.md)|입력을 datetime 스칼라로 변환 합니다.|
|[totimespan()](totimespanfunction.md)|입력을 timespan 스칼라로 변환 합니다.|
|[unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)|Unix epoch 마이크로초를 UTC 날짜/시간으로 변환 합니다.|
|[unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)|Unix epoch 밀리초를 UTC 날짜/시간으로 변환 합니다.|
|[unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)|Unix epoch 나노초를 UTC 날짜/시간으로 변환 합니다.|
|[unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)|Unix epoch 초를 UTC 날짜/시간으로 변환 합니다.|
|[weekofyear()](weekofyearfunction.md)|주 번호를 나타내는 정수를 반환 합니다.|


## <a name="dynamicarray-functions"></a>동적/배열 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat ()](arrayconcatfunction.md)|여러 동적 배열을 단일 배열에 연결 합니다.|
|[array_iif()](arrayifffunction.md)|배열에 요소 단위 iif 함수를 적용 합니다.|
|[array_index_of()](arrayindexoffunction.md)|배열에서 지정 된 항목을 검색 하 여 해당 위치를 반환 합니다.|
|[array_length ()](arraylengthfunction.md)|동적 배열의 요소 수를 계산 합니다.|
|[array_rotate_left()](array_rotate_leftfunction.md)|동적 배열 내의 값을 왼쪽으로 회전 합니다.|
|[array_rotate_right()](array_rotate_rightfunction.md)|동적 배열 내의 값을 오른쪽으로 회전 합니다.|
|[array_shift_left()](array_shift_leftfunction.md)|동적 배열 내의 값을 왼쪽으로 이동 합니다.|
|[array_shift_right()](array_shift_rightfunction.md)|동적 배열 내의 값을 오른쪽으로 이동 합니다.|
|[array_slice ()](arrayslicefunction.md)|동적 배열의 조각을 추출 합니다.|
|[array_split()](arraysplitfunction.md)|입력 배열에서 분할 된 배열의 배열을 빌드합니다.|
|[bag_keys()](bagkeysfunction.md)|동적 속성 모음 개체의 모든 루트 키를 열거 합니다.|
|[bag_merge()](bag-merge-function.md)|모든 속성을 병합 하 여 동적 속성 모음에 동적 속성 모음을 병합 합니다.|
|[bag_remove_keys()](bag-remove-keys-function.md)|동적 속성 모음에서 키 및 연결 된 값을 제거 합니다.|
|[pack()](packfunction.md)|이름 및 값 목록에서 동적 개체 (속성 모음)를 만듭니다.|
|[pack_all()](packallfunction.md)|테이블 형식 식의 모든 열에서 동적 개체 (속성 모음)를 만듭니다.|
|[pack_array()](packarrayfunction.md)|모든 입력 값을 동적 배열로 압축 합니다.|
|[repeat()](repeatfunction.md)|일련의 동일한 값을 포함 하는 동적 배열을 생성 합니다.|
|[set_difference()](setdifferencefunction.md)|첫 번째 배열에 있지만 다른 배열에는 없는 모든 고유 값 집합의 배열을 반환 합니다.|
|[set_has_element()](sethaselementfunction.md)|지정 된 배열에 지정 된 요소가 포함 되어 있는지 여부를 확인 합니다.|
|[set_intersect()](setintersectfunction.md)|모든 배열에 있는 모든 고유 값 집합의 배열을 반환 합니다.|
|[set_union()](setunionfunction.md)|제공 된 배열에 있는 모든 고유 값 집합의 배열을 반환 합니다.|
|[treepath()](treepathfunction.md)|동적 개체의 리프를 식별하는 모든 path 식을 열거합니다.|
|[zip()](zipfunction.md)|Zip 함수는 임의 개수의 동적 배열을 허용 합니다. 요소가 동일한 인덱스의 입력 배열 요소를 포함 하는 배열을 포함 하는 배열을 반환 합니다.|

## <a name="window-scalar-functions"></a>창 스칼라 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[다음 ()](nextfunction.md)|Serialize 된 행 집합의 경우 오프셋에 따라 이후 행에서 지정 된 열의 값을 반환 합니다.|
|[prev()](prevfunction.md)|Serialize 된 행 집합의 경우 오프셋에 따라 이전 행에서 지정 된 열의 값을 반환 합니다.|
|[row_cumsum()](rowcumsumfunction.md)|열의 누적 합계를 계산 합니다.|
|[row_number()](rownumberfunction.md)|지정 된 인덱스에서 시작 하 여 직렬화 된 행 집합에서 연속 하는 행의 수를 반환 하거나, 기본적으로 1을 반환 합니다.|

## <a name="flow-control-functions"></a>흐름 제어 함수

|함수 이름            |설명:                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|계산 된 식의 스칼라 상수 값을 반환 합니다.|

## <a name="mathematical-functions"></a>수학 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[abs ()](abs-function.md)|입력의 절대값을 계산 합니다.|
|[acos ()](acosfunction.md)|코사인이 지정 된 숫자 (cos ()의 역 연산) 인 각도를 반환 합니다.|
|[asin ()](asinfunction.md)|사인이 지정 된 숫자 (sin ()의 역 연산) 인 각도를 반환 합니다.|
|[atan ()](atanfunction.md)|탄젠트가 지정 된 숫자 (tan ()의 역 연산) 인 각도를 반환 합니다.|
|[atan2()](atan2function.md)|양수 x 축과 원점에서 점 (y, x) 까지의 광선 사이의 각도를 라디안으로 계산 합니다.|
|[beta_cdf()](beta-cdffunction.md)|표준 누적 베타 분포 함수를 반환 합니다.|
|[beta_inv()](beta-invfunction.md)|베타 누적 확률 베타 밀도 함수의 역을 반환 합니다.|
|[beta_pdf()](beta-pdffunction.md)|확률 밀도 베타 함수를 반환 합니다.|
|[cos ()](cosfunction.md)|코사인 함수를 반환 합니다.|
|[cot ()](cotfunction.md)|지정 된 각도의 삼각 코탄젠트를 라디안으로 계산 합니다.|
|[도 ()](degreesfunction.md)|수식도 = (180/PI) * 각도 단위를 사용 하 여 라디안의 각도 값을도 값으로 변환 합니다.|
|[exp ()](exp-function.md)|X의 밑이 e 인 지 수 함수 이며 e는 거듭제곱 x: e ^ x입니다.|
|[exp10()](exp10-function.md)|X의 밑이 10 인 지 수 함수 이며, 10은 거듭제곱 x: 10 ^ x에 발생 합니다.|
|[exp2()](exp2-function.md)|X의 밑이 2 인 지 수 함수 이며, 2는 거듭제곱 x: 2 ^ x입니다.|
|[gamma()](gammafunction.md)|감마 함수를 계산 합니다.|
|[isfinite()](isfinitefunction.md)|입력이 유한 값 (무한 또는 NaN) 인지 여부를 반환 합니다.|
|[isinf()](isinffunction.md)|입력이 무한 (양수 또는 음수) 값 인지 여부를 반환 합니다.|
|[isnan()](isnanfunction.md)|입력이 NaN (숫자가 아님) 값 인지 여부를 반환 합니다.|
|[log ()](log-function.md)|자연 로그 함수를 반환 합니다.|
|[log10 ()](log10-function.md)|일반 (밑수 10) 로그 함수를 반환 합니다.|
|[log2()](log2-function.md)|밑이 2 인 로그 함수를 반환 합니다.|
|[loggamma()](loggammafunction.md)|감마 함수의 절대값 로그를 계산 합니다.|
|[not()](notfunction.md)|Bool 인수의 값을 반대로 바꿉니다.|
|[pi ()](pifunction.md)|Pi (π)의 상수 값을 반환 합니다.|
|[pow()](powfunction.md)|거듭제곱 한 결과를 반환 합니다.|
|[라디안 ()](radiansfunction.md)|각도 단위 = (PI/180) * 각도 단위를 사용 하 여 각도의 각도 값을 라디안으로 값으로 변환 합니다.|
|[rand ()](randfunction.md)|난수를 반환 합니다.|
|[range ()](rangefunction.md)|간격이 동일한 일련의 값을 포함 하는 동적 배열을 생성 합니다.|
|[round ()](roundfunction.md)|지정 된 전체 자릿수로 반올림 된 원본을 반환 합니다.|
|[sign ()](signfunction.md)|숫자 식의 부호입니다.|
|[sin ()](sinfunction.md)|사인 함수를 반환 합니다.|
|[sqrt ()](sqrtfunction.md)|제곱근 함수를 반환 합니다.|
|[황갈색 ()](tanfunction.md)|탄젠트 함수를 반환 합니다.|
|[welch_test()](welch-testfunction.md)|[Welch 함수의](https://en.wikipedia.org/wiki/Welch%27s_t-test)p 값을 계산 합니다.|

## <a name="metadata-functions"></a>메타데이터 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|는 열 이름을 문자열로 사용 하 고 기본값을 사용 합니다. 열이 있으면 해당 열에 대 한 참조를 반환 하 고, 그렇지 않으면 기본값을 반환 합니다.|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|쿼리를 실행 하는 현재 클러스터를 반환 합니다.|
|[current_database()](current-database-function.md)|범위에 있는 데이터베이스의 이름을 반환 합니다.|
|[current_principal()](current-principalfunction.md)|이 쿼리를 실행 하는 현재 보안 주체를 반환 합니다.|
|[current_principal_details()](current-principal-detailsfunction.md)|쿼리를 실행 하는 보안 주체의 세부 정보를 반환 합니다.|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|쿼리를 실행 하는 현재 보안 주체의 그룹 멤버 자격 또는 보안 주체 id를 확인 합니다.|
|[cursor_after()](cursorafterfunction.md)|커서의 이전 값 뒤에 수집 된 레코드에 액세스 하는 데 사용 됩니다.|
|[estimate_data_size()](estimate-data-sizefunction.md)|테이블 형식 식에서 선택한 열의 예상 데이터 크기를 반환 합니다.|
|[extent_id()](extentidfunction.md)|현재 레코드가 있는 데이터 분할 ("익스텐트")을 식별 하는 고유 식별자를 반환 합니다.|
|[extent_tags()](extenttagsfunction.md)|현재 레코드가 있는 데이터 분할 ("익스텐트")의 태그가 있는 동적 배열을 반환 합니다.|
|[ingestion_time ()](ingestiontimefunction.md)|레코드의 $IngestionTime 숨겨진 datetime 열 또는 null을 검색 합니다.|

## <a name="rounding-functions"></a>반올림 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다.|
|[bin_at()](binatfunction.md)|Bin의 시작점에 대 한 제어를 사용 하 여 값을 고정 크기 "bin"으로 내림 합니다. Bin 함수를 참조 하세요.|
|[상한 ()](ceilingfunction.md)|지정 된 숫자 식 보다 크거나 같은 최소 정수를 계산 합니다.|
|[floor ()](floorfunction.md)|값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다.|

## <a name="conditional-functions"></a>조건부 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[case()](casefunction.md)|조건자 목록을 평가 하 고 조건자가 충족 된 첫 번째 결과 식을 반환 합니다.|
|[coalesce()](coalescefunction.md)|식 목록을 계산 하 고 null이 아닌 첫 번째 (또는 문자열의 경우 비어 있지 않은) 식을 반환 합니다.|
|[iif ()/iff ()](iiffunction.md)|첫 번째 인수 (조건자)를 계산 하 고 두 번째 또는 세 번째 인수 값을 반환 합니다 .이는 조건자가 true (second) 또는 false (셋째)로 평가 되는지에 따라 달라 집니다.|
|[max_of()](max-offunction.md)|계산 된 여러 숫자 식의 최 댓 값을 반환 합니다.|
|[min_of()](min-offunction.md)|계산 된 여러 숫자 식의 최 솟 값을 반환 합니다.|

## <a name="series-element-wise-functions"></a>계열 요소 단위 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|두 숫자 계열 입력의 요소 덧셈을 계산 합니다.|
|[series_divide()](series-dividefunction.md)|두 숫자 계열 입력의 요소 단위 나누기를 계산 합니다.|
|[series_equals()](series-equalsfunction.md)|`==`두 개의 숫자 계열 입력의 요소 단위 같음 () 논리 연산을 계산 합니다.|
|[series_greater()](series-greaterfunction.md)|`>`두 숫자 계열 입력의 요소 수준 더 큰 () 논리 연산을 계산 합니다.|
|[series_greater_equals()](series-greater-equalsfunction.md)|`>=`두 숫자 계열 입력의 요소 단위 크거나 같음 () 논리 연산을 계산 합니다.|
|[series_less()](series-lessfunction.md)|`<`두 숫자 계열 입력의 요소 단위 less () 논리 연산을 계산 합니다.|
|[series_less_equals()](series-less-equalsfunction.md)|`<=`두 숫자 계열 입력의 요소 수준 작거나 같음 () 논리 연산을 계산 합니다.|
|[series_multiply()](series-multiplyfunction.md)|두 숫자 계열 입력의 요소 단위 곱하기를 계산 합니다.|
|[series_not_equals()](series-not-equalsfunction.md)|`!=`두 숫자 계열 입력의 요소 중심 같지 않음 () 논리 연산을 계산 합니다.|
|[series_subtract()](series-subtractfunction.md)|두 숫자 계열 입력의 요소 단위 빼기를 계산 합니다.|

## <a name="series-processing-functions"></a>계열 처리 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|시리즈를 구성 요소로 분해 합니다.|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|계열 분해를 기반으로 하는 계열의 비정상을 찾습니다.|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|계열 분해를 기반으로 예측 합니다.|
|[series_fill_backward()](series-fill-backwardfunction.md)|계열의 누락 값에 대 한 역방향 채우기 보간을 수행 합니다.|
|[series_fill_const()](series-fill-constfunction.md)|계열의 누락 값을 지정 된 상수 값으로 바꿉니다.|
|[series_fill_forward()](series-fill-forwardfunction.md)|계열의 누락 값에 대 한 전방 채우기 보간을 수행 합니다.|
|[series_fill_linear()](series-fill-linearfunction.md)|계열의 누락 값에 대 한 선형 보간을 수행 합니다.|
|[series_fft()](series-fft-function.md)|계열에 FFT (Fast 푸리에 변환)를 적용 합니다.|
|[series_fir()](series-firfunction.md)|계열에 유한 임펄스 응답 필터를 적용 합니다.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|계열에서 두 세그먼트 선형 회귀를 적용 하 여 여러 열을 반환 합니다.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|계열에서 두 세그먼트 선형 회귀를 적용 하 여 동적 개체를 반환 합니다.|
|[series_fit_line()](series-fit-linefunction.md)|계열에 선형 회귀를 적용 하 여 여러 열을 반환 합니다.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|계열에 선형 회귀를 적용 하 여 동적 개체를 반환 합니다.|
|[series_fit_poly ()](series-fit-poly-function.md)|여러 열을 반환 하 여 계열에 다항식 회귀를 적용 합니다.|
|[series_ifft()](series-ifft-function.md)|계열에 역 IFFT (빠른 푸리에 변환)를 적용 합니다.|
|[series_iir()](series-iirfunction.md)|계열에 무한 임펄스 응답 필터를 적용 합니다.|
|[series_outliers()](series-outliersfunction.md)|계열에서 변칙 점수를 점수가 있습니다.|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|두 계열의 피어슨 상관 계수를 계산 합니다.|
|[series_periods_detect()](series-periods-detectfunction.md)|시계열에 존재 하는 가장 중요 한 기간을 찾습니다.|
|[series_periods_validate()](series-periods-validatefunction.md)|시계열에 지정 된 길이의 정기적인 패턴이 포함 되어 있는지 여부를 확인 합니다.|
|[series_seasonal()](series-seasonalfunction.md)|계열의 계절 구성 요소를 찾습니다.|
|[series_stats()](series-statsfunction.md)|여러 열의 계열에 대 한 통계를 반환 합니다.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|동적 개체의 계열에 대 한 통계를 반환 합니다.|

## <a name="string-functions"></a>문자열 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|문자열을 base64 문자열로 인코딩합니다.|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|Base64 문자열을 UTF-8 문자열로 디코딩합니다.|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|Base64 문자열을 long 값 배열로 디코딩합니다.|
|[countof()](countoffunction.md)|문자열의 부분 문자열 발생을 계산합니다. 일반 문자열 일치는 겹칠 수 있습니다. regex는 그렇지 않습니다.|
|[extract()](extractfunction.md)|텍스트 문자열에서 정규식 에 대한 일치 항목을 가져옵니다.|
|[extract_all()](extractallfunction.md)|텍스트 문자열에서 정규식에 대 한 모든 일치 항목을 가져옵니다.|
|[extractjson()](extractjsonfunction.md)|path 식을 사용하여 JSON 텍스트에서 지정된 요소를 가져옵니다.|
|[indexof ()](indexoffunction.md)|함수는 입력 문자열에서 맨 처음 발견 되는 지정 된 문자열의 0부터 시작 하는 인덱스를 보고 합니다.|
|[isempty()](isemptyfunction.md)|인수가 빈 문자열 이거나 null 이면 true를 반환 합니다.|
|[isnotempty()](isnotemptyfunction.md)|인수가 빈 문자열 또는 null이 아닌 경우 true를 반환 합니다.|
|[isnotnull()](isnotnullfunction.md)|인수가 null이 아닌 경우 true를 반환 합니다.|
|[isnull ()](isnullfunction.md)|유일한 인수를 계산 하 고 인수가 null 값으로 계산 되는지를 나타내는 부울 값을 반환 합니다.|
|[parse_command_line()](parse-command-line.md)|유니코드 명령줄 문자열을 구문 분석 하 고 명령줄 인수의 배열을 반환 합니다.|
|[parse_csv()](parsecsvfunction.md)|쉼표로 구분 된 값을 나타내는 지정 된 문자열을 분할 하 고 이러한 값을 포함 하는 문자열 배열을 반환 합니다.|
|[parse_ipv4()](parse-ipv4function.md)|입력을 long (부호 있는 64 비트) 숫자 표현으로 변환 합니다.|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|입력 문자열 및 IP 접두사 마스크를 long (부호 있는 64 비트) 숫자 표현으로 변환 합니다.|
|[parse_ipv6()](parse-ipv6function.md)|IPv6 또는 IPv4 문자열을 정식 IPv6 문자열 표현으로 변환 합니다.|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|IPv6 또는 IPv4 문자열과 네트워크 마스크를 정식 IPv6 문자열 표현으로 변환 합니다.|
|[parse_json()](parsejsonfunction.md)|는 문자열을 JSON 값으로 해석 하 고 값을 동적으로 반환 합니다.|
|[parse_url()](parseurlfunction.md)|절대 URL 문자열을 구문 분석 하 고 URL의 모든 부분을 포함 하는 동적 개체를 반환 합니다.|
|[parse_urlquery()](parseurlqueryfunction.md)|Url 쿼리 문자열을 구문 분석 하 고 쿼리 매개 변수를 포함 하는 동적 개체를 반환 합니다.|
|[parse_version()](parse-versionfunction.md)|버전의 입력 문자열 표현을 비교할 수 있는 10 진수로 변환 합니다.|
|[replace ()](replacefunction.md)|모든 정규식 일치 항목을 다른 문자열로 바꿉니다.|
|[reverse ()](reversefunction.md)|함수는 입력 문자열을 역순으로 만듭니다.|
|[split()](splitfunction.md)|지정 된 구분 기호에 따라 지정 된 문자열을 분할 하 고 포함 된 부분 문자열을 포함 하는 문자열 배열을 반환 합니다.|
|[strcat()](strcatfunction.md)|1에서 64 사이의 인수를 연결 합니다.|
|[strcat_delim()](strcat-delimfunction.md)|첫 번째 인수로 제공 되는 구분 기호를 사용 하 여 2 및 64 인수를 연결 합니다.|
|[strcmp()](strcmpfunction.md)|두 문자열을 비교합니다.|
|[strlen()](strlenfunction.md)|입력 문자열의 길이 (문자)를 반환 합니다.|
|[strrep()](strrepfunction.md)|지정 된 문자열을 지정 된 횟수 만큼 반복 합니다 (기본값-1).|
|[substring ()](substringfunction.md)|일부 인덱스부터 문자열 끝 까지의 소스 문자열에서 부분 문자열을 추출 합니다.|
|[toupper()](toupperfunction.md)|문자열을 대문자로 변환합니다.|
|[translate()](translatefunction.md)|지정 된 문자열에서 문자 집합 (' searchList ')을 다른 문자 집합 (' replacementList ')으로 바꿉니다.|
|[trim ()](trimfunction.md)|지정 된 정규식에 대 한 선행 및 후행 일치 항목을 모두 제거 합니다.|
|[trim_end()](trimendfunction.md)|지정 된 정규식의 후행 일치 항목을 제거 합니다.|
|[trim_start()](trimstartfunction.md)|지정 된 정규식의 선행 일치를 제거 합니다.|
|[url_decode()](urldecodefunction.md)|함수는 인코딩된 URL을 일반 URL 표현으로 변환 합니다.|
|[url_encode()](urlencodefunction.md)|함수는 입력 URL의 문자를 인터넷을 통해 전송할 수 있는 형식으로 변환 합니다.|

## <a name="ipv4ipv6-functions"></a>IPv4/IPv6 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|두 IPv4 문자열을 비교 합니다.|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|두 IPv4 문자열을 찾습니다.|
|[parse_ipv4()](parse-ipv4function.md)|입력 문자열을 long (부호 있는 64 비트) 숫자 표현으로 변환 합니다.|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|입력 문자열 및 IP 접두사 마스크를 long (부호 있는 64 비트) 숫자 표현으로 변환 합니다.|
|[ipv6_compare()](ipv6-comparefunction.md)|두 IPv4 또는 IPv6 문자열을 비교 합니다.|
|[ipv6_is_match()](ipv6-is-matchfunction.md)|두 IPv4 또는 IPv6 문자열을 찾습니다.|
|[parse_ipv6()](parse-ipv6function.md)|IPv6 또는 IPv4 문자열을 정식 IPv6 문자열 표현으로 변환 합니다.|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|IPv6 또는 IPv4 문자열과 네트워크 마스크를 정식 IPv6 문자열 표현으로 변환 합니다.|
|[format_ipv4()](format-ipv4-function.md)|네트워크 마스크를 사용 하 여 입력을 구문 분석 하 고 IPv4 주소를 나타내는 문자열을 반환 합니다.|
|[format_ipv4_mask()](format-ipv4-mask-function.md)|네트워크 마스크를 사용 하 여 입력을 구문 분석 하 고 IPv4 주소를 나타내는 문자열을 CIDR 표기법으로 반환 합니다.|

## <a name="type-functions"></a>형식 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|단일 인수의 런타임 형식을 반환 합니다.|

## <a name="scalar-aggregation-functions"></a>스칼라 집계 함수

|함수 이름     |설명:                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|Hll 또는 hll 병합에 의해 생성 된 hll 결과에서 dcount를 계산 합니다.|
|[hll_merge()](hllmergefunction.md)|Hll 결과 (집계 버전의 스칼라 버전 hll-merge ())를 병합 합니다.|
|[percentile_tdigest()](percentile-tdigestfunction.md)|Tdigest 결과 (tdigest 또는 merge-tdigest에 의해 생성 됨)에서 백분위 수 결과를 계산 합니다.|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|데이터 집합에 있는 값의 백분율 순위를 계산 합니다.|
|[rank_tdigest()](rank-tdigest.md)|집합에 있는 값의 상대 순위를 계산 합니다.|
|[tdigest_merge()](tdigest-mergefunction.md)|Tdigest 결과 (집계 버전 tdigest-merge ())의 스칼라 버전을 병합 합니다.|

## <a name="geospatial-functions"></a>지리 공간적 함수

|함수 이름|설명:|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|지구에서 두 지리 공간적 좌표 사이의 최단 거리를 계산 합니다.|
|[geo_distance_point_to_line()](geo-distance-point-to-line-function.md)|좌표와 지구 선 사이의 최단 거리를 계산 합니다.|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|지리 공간적 좌표가 지구에 원 안에 있는지 여부를 계산 합니다.|
|[geo_point_in_polygon()](geo-point-in-polygon-function.md)|지리 공간적 좌표가 다각형 내부에 있는지 아니면 지구에 multipolygon 인지를 계산 합니다.|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|지리적 위치에 대 한 Geohash 문자열 값을 계산 합니다.|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|Geohash 사각형 영역의 중심을 나타내는 지리 공간적 좌표를 계산 합니다.|
|[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)|지리적 위치에 대 한 S2 셀 토큰 문자열 값을 계산 합니다.|
|[geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md)|S2 셀의 중심을 나타내는 지리 공간적 좌표를 계산 합니다.|
|[geo_polygon_to_s2cells()](geo-polygon-to-s2cells-function.md)|지구에 다각형 또는 multipolygon을 포함 하는 S2 셀 토큰을 계산 합니다. 유용한 지리 공간적 조인 도구입니다.|
|[geo_line_densify()](geo-line-densify-function.md)|중간 요소를 추가 하 여 평면 선 가장자리를 geodesics으로 변환 합니다.|
|[geo_polygon_densify()](geo-polygon-densify-function.md)|중간 요소를 추가 하 여 polygon 또는 multipolygon 평면 가장자리를 geodesics으로 변환 합니다.|

## <a name="hash-functions"></a>해시 함수

|함수 이름|설명:|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[hash ()](hashfunction.md)|입력 값에 대 한 해시 값을 반환 합니다.|
|[hash_combine()](hash_combinefunction.md)|두 개 이상의 해시 값을 결합 합니다.|
|[hash_many()](hash_manyfunction.md)|여러 값의 결합 된 해시 값을 반환 합니다.|
|[hash_md5()](md5hashfunction.md)|입력 값에 대 한 MD5 해시 값을 반환 합니다.|
|[hash_sha256()](sha256hashfunction.md)|입력 값의 SHA256 해시 값을 반환 합니다.|
