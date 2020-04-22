---
title: 파이썬 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 파이썬 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5192474779fb712595ff1c25785892bb543fda84
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765718"
---
# <a name="python-plugin"></a>파이썬 플러그인

::: zone pivot="azuredataexplorer"

파이썬 플러그인은 파이썬 스크립트를 사용하여 사용자 정의 함수 (UDF)를 실행합니다. Python 스크립트는 테이블 형식 데이터를 입력으로 가져옵니다.
플러그인의 런타임은 클러스터의 노드에서 실행되는 [샌드박스에서](../concepts/sandboxes.md)호스팅됩니다.

## <a name="syntax"></a>구문

*T* `|` `hint.distribution` `=` `single` | `,` *script* `,` *script_parameters*`,` *external_artifacts* *output_schema* [()] `python(`output_schema 스크립트 [ script_parameters][ external_artifacts]`per_node` `evaluate``)`

## <a name="arguments"></a>인수

* *output_schema*: `type` Python 코드에서 반환되는 테이블 형식 데이터의 출력 스키마를 정의하는 리터럴입니다.
    * 형식은 다음과 `typeof(`같은가: *열 이름* `:` *열 유형* [, ...] `)`예를 들어: `typeof(col1:string, col2:long)`.
    * 입력 스키마를 확장하기 위해 다음 구문을 사용합니다.`typeof(*, col1:string, col2:long)`
* *스크립트*: `string` 실행될 유효한 파이썬 스크립트인 리터럴입니다.
* *script_parameters*: `dynamic` 예약 `kargs` 된 사전으로 Python 스크립트에 전달 할 이름 / 값 쌍의 속성 가방인 선택적 리터럴입니다 [(예약 된 파이썬 변수](#reserved-python-variables)참조).
* *hint.distribution*: 플러그인의 실행을 위한 선택적 힌트로 여러 클러스터 노드에 분산됩니다.
  * 기본값은 `single`입니다.
  * `single`: 스크립트의 단일 인스턴스가 전체 쿼리 데이터를 통해 실행됩니다.
  * `per_node`: Python 블록이 분산되기 전에 쿼리가 분산되면 스크립트의 인스턴스가 포함된 데이터를 통해 각 노드에서 실행됩니다.
* *external_artifacts*: `dynamic` 클라우드 저장소에서 액세스할 수 있고 런타임에 스크립트를 사용할 수 있는 아티팩트에 대한 이름 & URL 쌍의 속성 백인 선택적 리터럴입니다.
  * 이 속성 가방에 참조되는 URL은 다음을 수행해야 합니다.
  * 클러스터의 [콜아웃 정책에](../management/calloutpolicy.md)포함됩니다.
    2. [저장소 연결 문자열에](../api/connection-strings/storage.md)설명된 대로 공개적으로 사용 가능한 위치에 있거나 필요한 자격 증명을 제공합니다.
  * 아티팩트는 스크립트가 로컬 임시 디렉터리에서 `.\Temp`사용할 수 있도록 제공되며 속성 가방에 제공된 이름은 로컬 파일 이름으로 사용됩니다(아래 [예제](#examples) 참조).
  * 자세한 내용은 아래 [부록을](#appendix-installing-packages-for-the-python-plugin) 참조하십시오.

## <a name="reserved-python-variables"></a>예약된 파이썬 변수

다음 변수는 Kusto 쿼리 언어와 Python 코드 간의 상호 작용을 위해 예약되어 있습니다.

* `df`: 입력 테이블 형식 `T` 데이터(위의 값)를 DataFrame으로 `pandas` 사용합니다.
* `kargs`: *파이썬* 사전으로 script_parameters 인수의 값입니다.
* `result`: `pandas` 파이썬 스크립트에서 만든 DataFrame으로 그 값이 플러그인 다음에 Kusto 쿼리 연산자로 전송되는 테이블 형식 데이터가 됩니다.

## <a name="onboarding"></a>온보딩

* 플러그인은 기본적으로 비활성화되어 있습니다.
* 플러그인을 활성화하기 위한 필수 구성 사항은 [여기에](../concepts/sandboxes.md#prerequisites)나열되어 있습니다.
* [클러스터의 **구성** 탭에서 Azure 포털의 플러그인을](https://docs.microsoft.com/azure/data-explorer/language-extensions)활성화 하거나 사용하지 않도록 설정합니다.

## <a name="notes-and-limitations"></a>참고 사항 및 제한 사항

* 파이썬 샌드박스 이미지는 *파이썬 3.6* 엔진이 있는 *아나콘다 5.2.0* 배포판을 기반으로 합니다.
  패키지 목록은 [여기에서](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) 찾을 수 있습니다 (패키지의 작은 비율은 플러그인이 실행되는 샌드 박스에 의해 적용 제한과 호환되지 않을 수 있습니다).
* 파이썬 이미지에는 일반적인 ML 패키지도 `hdbscan`포함되어 `xgboost` 있습니다. `tensorflow` `keras` `torch`
* 플러그인은 기본적으로 (로) `np`팬더 (로) `pd` *& numpy를* *수입합니다.*  필요에 따라 다른 모듈을 가져올 수 있습니다.
* **쿼리 및 업데이트 [정책의](../management/updatepolicy.md) [인제](../management/data-ingestion/ingest-from-query.md)**
  * 다음과 같은 쿼리에서 플러그인을 사용할 수 있습니다.
      1. 소스 테이블이 *비스트리밍* 섭취를 사용하여 인제화되는 업데이트 정책의 일부로 정의됩니다.
      2. 쿼리에서 인더스트하는 명령의 일부로 실행합니다(예: `.set-or-append`).
  * 위의 두 경우 모두, 수집의 볼륨과 빈도뿐만 아니라 Python 논리의 복잡성 및 리소스 사용률이 [샌드박스 제한](../concepts/sandboxes.md#limitations)및 클러스터의 사용 가능한 리소스와 일치하는지 확인하는 것이 좋습니다.
    이렇게 하지 않으면 [제한 오류가](../concepts/sandboxes.md#errors)발생할 수 있습니다.
  * 스트리밍 을 사용하여 소스 테이블을 [인제화하는](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)업데이트 정책의 일부로 정의된 쿼리에서 플러그인을 사용할 수 *없습니다.*

## <a name="examples"></a>예

```kusto
range x from 1 to 360 step 1
| evaluate python(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result = df\n'                     //  The Python decorated script
'n = df.shape[0]\n'
'g = kargs["gain"]\n'
'f = kargs["cycles"]\n'
'result["fx"] = g * np.sin(df["x"]/n*2*np.pi*f)\n'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```
:::image type="content" source="images/samples/sine-demo.png" alt-text="사인 데모":::

```kusto
print "This is an example for using 'external_artifacts'"
| evaluate python(
    typeof(File:string, Size:string),
    "import os\n"
    "result = pd.DataFrame(columns=['File','Size'])\n"
    "sizes = []\n"
    "path = '.\\\\Temp'\n"
    "files = os.listdir(path)\n"
    "result['File']=files\n"
    "for file in files:\n"
    "    sizes.append(os.path.getsize(path + '\\\\' + file))\n"
    "result['Size'] = sizes\n"
    "\n",
    external_artifacts = 
        dynamic({"this_is_my_first_file":"https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r",
                 "this_is_a_script":"https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py"})
)
```

| 파일                  | 크기 |
|-----------------------|------|
| this_is_a_script      | 120  |
| this_is_my_first_file | 105  |

## <a name="performance-tips"></a>성능 팁

* 플러그인의 입력 데이터 집합을 필요한 최소 금액(열/행)으로 줄입니다.
    * 가능한 경우 Kusto의 쿼리 언어와 함께 원본 데이터 집합에서 필터를 사용합니다.
    * 소스 열의 하위 집합에서 계산을 수행하려면 플러그인을 호출하기 전에 해당 열만 투영합니다.
* 스크립트의 논리가 배포가능한 경우 사용할 `hint.distribution = per_node` 수 있습니다.
    * 입력 데이터 집합을 분할하는 데 [파티션 연산자도](partitionoperator.md) 사용할 수 있습니다.
* 가능한 한 Kusto의 쿼리 언어를 사용하여 Python 스크립트의 논리를 구현합니다.

    예:

    ```kusto    
    .show operations
    | where StartedOn > ago(7d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node python( // Using per_node distribution, as the script's logic allows it
        typeof(*, _2d:double),
        'result = df\n'
        'result["_2d"] = 2 * df["d_seconds"]\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(_2d)
    ```

## <a name="usage-tips"></a>사용 팁

* 에서 `Kusto.Explorer`파이썬 스크립트를 포함하는 다중 줄 문자열을 생성하려면 좋아하는 파이썬 편집기 *(Jupyter*, Visual Studio *Code,* *PyCharm*등)에서 파이썬 스크립트를 복사한 다음 다음 중 하나를 수행하십시오.
    * *F2를* 눌러 **파이썬 창에서 편집을** 엽니다. 스크립트를 이 창에 붙여넣습니다. **확인**을 선택합니다. 스크립트는 따옴표와 새 줄로 장식되고 (Kusto에서 유효하므로) 쿼리 탭에 자동으로 붙여 넣습니다.
    * 파이썬 코드를 쿼리 탭에 직접 붙여 넣고 해당 줄을 선택하고 *Ctrl+K,* *Ctrl+S* 핫 키를 눌러 위와 같이 *장식합니다(Ctrl+K*, *Ctrl+M* 핫 키를 반대로 누르십시오). [다음은](../tools/kusto-explorer-shortcuts.md#query-editor) 쿼리 편집기 바로 가기의 전체 목록입니다.
* Kusto 문자열 구분 기호와 파이썬 문자열 리터럴 간의 충돌을 방지하려면`'`Kusto 쿼리의 Kusto 문자열 리터럴에 대해 단일`"`따옴표 문자 () 및 파이썬 스크립트의 파이썬 문자열 리터럴에 대해 두 면문자 문자 ()를 사용하는 것이 좋습니다.
* 외부 [데이터 연산자를](externaldata-operator.md) 사용하여 Azure Blob 저장소와 같은 외부 위치에 저장한 스크립트의 콘텐츠를 가져옵니다.
  
    **예제**

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate python(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

## <a name="appendix-installing-packages-for-the-python-plugin"></a>부록 : 파이썬 플러그인패키지 설치

다음과 같은 이유로 인해 패키지를 자체 설치해야 할 수 있습니다.

* 패키지는 비공개이며 사용자 고유의 패키지입니다.
* 패키지는 공용이지만 플러그인의 기본 이미지에는 포함되지 않습니다.

다음 단계를 수행하여 패키지를 설치할 수 있습니다.

1. 일회성 전제 조건:
  
  a. Blob 컨테이너를 만들어 패키지를 호스트하는 것이 바람직하게는 클러스터와 동일한 영역에서 호스팅합니다.
    * 예: https://artifcatswestus.blob.core.windows.net/python (클러스터가 미국 서부에 있다고 가정)
  
  b. 클러스터의 [콜아웃 정책을](../management/calloutpolicy.md) 변경하여 해당 위치에 액세스할 수 있도록 합니다.
    * 이렇게 하려면 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요합니다.
    * 예를 들어 에 있는 Blob에 https://artifcatswestus.blob.core.windows.net/python대한 액세스를 활성화하려면 실행할 명령이 다음과 다 됩니다.

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. 공개 [패키지(PyPi](https://pypi.org/) 또는 기타 채널)의 경우. 패키지 및 해당 종속성을 다운로드합니다.
  b. 필요한 경우 wheel ()`*.whl`파일로 컴파일하십시오.
    * cmd 창에서 (로컬 파이썬 환경에서) 실행 :
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. 필요한 패키지와 해당 종속성을 포함하는 zip 파일을 만듭니다.

    * 공용 패키지의 경우: 이전 단계에서 다운로드한 파일을 압축합니다.
    * 참고:
        * 상위 폴더가 `.whl` *아닌* 파일 자체의 압축을 지퍼로 만들어야 합니다.
        * 기본 샌드박스 이미지에서 동일한 버전으로 이미 존재하는 패키지에 대한 파일을 건너뛸 `.whl` 수 있습니다.
    * 개인 패키지의 경우: 패키지의 폴더와 해당 종속성의 폴더를 압축합니다.

4. 지퍼가 진 파일을 1단계부터 아티팩트 위치의 Blob에 업로드합니다.

5. 플러그인 `python` 호출:
    * 속성 `external_artifacts` 이름 가방과 zip 파일(Blob의 URL)에 대한 참조가 있는 매개변수를 지정합니다.
    * 인라인 파이썬 코드에서 `Zipackage` `sandbox_utils` : zip `install()` 파일의 이름으로 메서드를 가져 와서 호출합니다.

### <a name="example"></a>예제

가짜 데이터를 생성하는 [Faker](https://pypi.org/project/Faker/) 패키지 설치:

```kusto
range Id from 1 to 3 step 1 
| extend Name=''
| evaluate python(typeof(*),
    'from sandbox_utils import Zipackage\n'
    'Zipackage.install("Faker.zip")\n'
    'from faker import Faker\n'
    'fake = Faker()\n'
    'result = df\n'
    'for i in range(df.shape[0]):\n'
    '    result.loc[i, "Name"] = fake.name()\n',
    external_artifacts=pack('faker.zip', 'https://artifacts.blob.core.windows.net/kusto/Faker.zip?...'))
```

| Id | 이름         |
|----|--------------|
|   1| 게리 타피아   |
|   2| 엠마 에번스   |
|   3| 애슐리 보웬 |

---

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
