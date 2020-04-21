---
title: R 플러그인 (미리 보기) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 R 플러그인(미리 보기)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 06655cc260f23b8812450513f85bec63c33f1775
ms.sourcegitcommit: 857e7062c00a3ccff3c7085375d5c077936afaa5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524415"
---
# <a name="r-plugin-preview"></a>R 플러그인(미리 보기)

::: zone pivot="azuredataexplorer"

R 플러그인은 R 스크립트를 사용하여 사용자 정의 함수(UDF)를 실행합니다. R 스크립트는 테이블 형식 데이터를 입력으로 가져옵니다 및 테이블 형식 출력을 생성 할 것으로 예상됩니다.
플러그인의 런타임은 클러스터의 노드에서 실행되는 격리되고 안전한 환경인 [샌드박스에서](../concepts/sandboxes.md)호스팅됩니다.

### <a name="syntax"></a>구문

*T* `|` `hint.distribution` `=` `single` | `,` *script* `,` *script_parameters* *output_schema* [()] `r(`output_schema 스크립트 [ script_parameters]`per_node` `evaluate``)`


### <a name="arguments"></a>인수

* *output_schema*: `type` R 코드에서 반환되는 테이블 형식 데이터의 출력 스키마를 정의하는 리터럴입니다.
    * 형식은 다음과 `typeof(`같은가: *열 이름* `:` *열 유형* [, ...] `)`예를 들어: `typeof(col1:string, col2:long)`.
    * 입력 스키마를 확장하려면 다음 구문을 `typeof(*, col1:string, col2:long)`사용합니다.
* *스크립트*: `string` 실행될 유효한 R 스크립트인 리터럴입니다.
* *script_parameters*: `dynamic` 예약 `kargs` 된 사전으로 R 스크립트에 전달 할 이름 / 값 쌍의 속성 가방인 선택적 리터럴입니다 [(예약 R 변수](#reserved-r-variables)참조).
* *hint.distribution*: 플러그인의 실행을 위한 선택적 힌트로 여러 클러스터 노드에 분산됩니다.
   기본값: `single`.
    * `single`: 스크립트의 단일 인스턴스가 전체 쿼리 데이터를 통해 실행됩니다.
    * `per_node`: R 블록이 분산되기 전에 쿼리가 분산되면 스크립트의 인스턴스가 포함된 데이터를 통해 각 노드에서 실행됩니다.


### <a name="reserved-r-variables"></a>예약된 R 변수

다음 변수는 Kusto 쿼리 언어와 R 코드 간의 상호 작용을 위해 예약되어 있습니다.

* `df`: R DataFrame으로 입력 `T` 된 테이블 형식 데이터 (위의 값).
* `kargs`: r 사전으로 *script_parameters* 인수의 값입니다.
* `result`: R 스크립트에 의해 생성된 R DataFrame으로, 그 값이 플러그인 다음에 오는 Kusto 쿼리 연산자로 전송되는 테이블 형식 데이터가 됩니다.

### <a name="onboarding"></a>온보딩


* 플러그인은 기본적으로 비활성화되어 있습니다.
    * *클러스터에서 플러그인을 사용하도록 설정하는 데 관심이 있으십니까?*
        
        * Azure 포털에서 Azure 데이터 탐색기 클러스터 내에서 왼쪽 메뉴에서 **새 지원 요청을** 선택합니다.
        * 플러그인을 사용하지 않도록 설정하려면 지원 티켓도 열어야 합니다.

### <a name="notes-and-limitations"></a>참고 사항 및 제한 사항

* R 샌드박스 이미지는 *Windows용 R 3.4.4를*기반으로 하며 [아나콘다의 R 에센셜 번들](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)패키지가 포함되어 있습니다.
* R 샌드박스는 네트워크에 액세스하는 것을 제한하므로 R 코드는 이미지에 포함되지 않은 추가 패키지를 동적으로 설치할 수 없습니다. 특정 패키지가 필요한 경우 Azure 포털에서 **새 지원 요청을** 엽니다.


### <a name="examples"></a>예

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```
![대체 텍스트](./images/samples/sine-demo.png "시네 데모")




### <a name="performance-tips"></a>성능 팁

* 플러그인의 입력 데이터 집합을 필요한 최소 금액(열/행)으로 줄입니다.
    * Kusto 쿼리 언어를 사용하여 가능하면 원본 데이터 집합에서 필터를 사용합니다.
    * 소스 열의 하위 집합에서 계산을 수행하려면 플러그인을 호출하기 전에 해당 열만 투영합니다.
* 스크립트의 논리가 배포가능한 경우 사용할 `hint.distribution = per_node` 수 있습니다.
    * 입력 데이터 집합을 분할하는 데 [파티션 연산자도](partitionoperator.md) 사용할 수 있습니다.
* 가능하면 Kusto 쿼리 언어를 사용하여 R 스크립트의 논리를 구현합니다.

    다음은 그 예입니다.

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

### <a name="usage-tips"></a>사용 팁

* Kusto 문자열 구분 기호와 R의 구분 기호 간의 충돌을 방지하려면`'`Kusto 쿼리에서 Kusto 문자열 리터럴에 대해 단일`"`따옴표 문자 () 및 R 스크립트의 R 문자열 리터럴에 대해 double quote 문자 ()를 사용하는 것이 좋습니다.
* 외부 [데이터 연산자를](externaldata-operator.md) 사용하여 Azure Blob 저장소, 공용 GitHub 리포지토리 등과 같은 외부 위치에 저장한 스크립트의 콘텐츠를 가져옵니다.
  
  다음은 그 예입니다.

    ```kusto    
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end

