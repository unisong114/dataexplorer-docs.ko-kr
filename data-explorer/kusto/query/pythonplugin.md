---
title: Python 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Python 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 984e5c37f3d29a6c56f88c6eb9b6750635e48920
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345983"
---
# <a name="python-plugin"></a>Python 플러그 인

::: zone pivot="azuredataexplorer"

Python 플러그 인은 Python 스크립트를 사용 하 여 UDF (사용자 정의 함수)를 실행 합니다. Python 스크립트는 테이블 형식 데이터를 입력으로 가져오며 테이블 형식 출력을 생성할 것으로 예상 됩니다.
플러그 인의 런타임은 클러스터의 노드에서 실행 되는 [샌드박스에서](../concepts/sandboxes.md)호스팅됩니다.

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `python(` *output_schema* `,` *스크립트* [ `,` *script_parameters*] [ `,` *external_artifacts*]`)`

## <a name="arguments"></a>인수

* *output_schema*: `type` Python 코드에서 반환 하는 테이블 형식 데이터의 출력 스키마를 정의 하는 리터럴입니다.
    * 형식은 `typeof(` *ColumnName* `:` *ColumnType*[, ...] `)` 입니다. 예를 들면 `typeof(col1:string, col2:long)` 입니다.
    * 입력 스키마를 확장 하려면 다음 구문을 사용 합니다.`typeof(*, col1:string, col2:long)`
* *script*: `string` 실행할 유효한 Python 스크립트에 해당 하는 리터럴입니다.
* *script_parameters*: 선택적 `dynamic` 리터럴입니다. 이름/값 쌍의 속성 모음으로 Python 스크립트에 예약 된 사전으로 전달 됩니다 `kargs` . 자세한 내용은 [예약 된 Python 변수](#reserved-python-variables)를 참조 하세요.
* *힌트. 배포*: 여러 클러스터 노드에 분산 될 플러그 인의 실행에 대 한 선택적 힌트입니다.
  * 기본값은 `single`입니다.
  * `single`: 스크립트의 단일 인스턴스는 전체 쿼리 데이터에 대해 실행 됩니다.
  * `per_node`: Python 블록 이전의 쿼리가 배포 되는 경우 스크립트의 인스턴스는 포함 된 데이터에 대해 각 노드에서 실행 됩니다.
* *external_artifacts*: `dynamic` 클라우드 저장소에서 액세스할 수 있는 아티팩트에 대 한 이름 및 URL 쌍의 속성 모음 인 선택적 리터럴입니다. 스크립트에서 런타임에 사용할 수 있도록 설정할 수 있습니다.
  * 이 속성 모음에서 참조 되는 Url은 다음과 같아야 합니다.
    * 클러스터의 [콜아웃 정책](../management/calloutpolicy.md)에 포함 되어 있습니다.
    * 공개적으로 사용 가능한 위치에서 또는 [저장소 연결 문자열](../api/connection-strings/storage.md)에 설명 된 대로 필요한 자격 증명을 제공 합니다.
  * 아티팩트는 스크립트가 로컬 임시 디렉터리인에서 사용할 수 있게 됩니다 `.\Temp` . 속성 모음에 제공 된 이름은 로컬 파일 이름으로 사용 됩니다. [예제](#examples)를 참조 하세요.
  * 자세한 내용은 [Python 플러그인에 대 한 패키지 설치](#install-packages-for-the-python-plugin)를 참조 하세요. 

## <a name="reserved-python-variables"></a>예약 된 Python 변수

다음 변수는 Kusto 쿼리 언어와 Python 코드 간의 상호 작용을 위해 예약 되어 있습니다.

* `df`: 입력 표 형식 데이터 ( `T` 위의 값)는 `pandas` 데이터 프레임입니다.
* `kargs`: Python 사전으로 *script_parameters* 된 인수의 값입니다.
* `result`: `pandas` Python 스크립트에서 만든 데이터 프레임 이며, 해당 값은 플러그 인을 따르는 Kusto 쿼리 연산자로 전송 되는 테이블 형식 데이터가 됩니다.

## <a name="enable-the-plugin"></a>플러그 인을 사용하도록 설정

* 플러그 인은 기본적으로 사용 하지 않도록 설정 되어 있습니다.
* 플러그 인을 사용 하도록 설정 하려면 [필수 구성 요소](../concepts/sandboxes.md#prerequisites)목록을 참조 하십시오.
* 클러스터의[구성 탭](../../language-extensions.md)에 있는 Azure Portal에서 플러그 인을 사용 하거나 사용 하지 않도록 설정 합니다.

## <a name="python-sandbox-image"></a>Python sandbox 이미지

* Python sandbox 이미지는 *python 3.6* 엔진과 *Anaconda 5.2.0* 분포를 기반으로 합니다.
  [Anaconda 패키지](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)의 목록을 참조 하세요.
  
  > [!NOTE]
  > 적은 비율의 패키지가 플러그 인이 실행 되는 샌드박스에서 적용 되는 제한과 호환 되지 않을 수 있습니다.
  
* Python 이미지에는 `tensorflow` ,, `keras` ,, `torch` `hdbscan` `xgboost` 및 기타 유용한 패키지와 같은 일반적인 ML 패키지도 포함 되어 있습니다.
* 플러그 인은 기본적으로 *numpy* (as `np` ) & *pandas* 를 가져옵니다 `pd` .  필요에 따라 다른 모듈을 가져올 수 있습니다.

## <a name="use-ingestion-from-query-and-update-policy"></a>쿼리 및 업데이트 정책에서 수집 사용

* 다음과 같은 쿼리에서 플러그 인을 사용 합니다.
  * 원본 테이블이 *비 스트리밍* 수집을 사용 하도록 수집 [업데이트 정책의](../management/updatepolicy.md)일부로 정의 됩니다.
  * 와 같은 [쿼리에서 수집](../management/data-ingestion/ingest-from-query.md)하는 명령의 일부로 실행 `.set-or-append` 합니다.
    이러한 두 경우 모두 수집의 볼륨 및 빈도와 Python 논리에서 사용 하는 복잡성 및 리소스를 [샌드박스 제한 사항](../concepts/sandboxes.md#limitations) 및 클러스터의 사용 가능한 리소스와 일치 하는지 확인 합니다. 그렇게 하지 않으면 [제한 오류가](../concepts/sandboxes.md#errors)발생할 수 있습니다.
* 원본 테이블이 [스트리밍](../../ingest-data-streaming.md)수집을 사용 하 여 수집 업데이트 정책의 일부로 정의 된 쿼리에는 플러그 인을 사용할 수 없습니다.

## <a name="examples"></a>예제

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

:::image type="content" source="images/plugin/sine-demo.png" alt-text="사인 데모" border="false":::

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

* 플러그 인의 입력 데이터 집합을 필요한 최소 크기 (열/행)로 줄입니다.
    * 가능 하면 Kusto의 쿼리 언어를 사용 하 여 원본 데이터 집합에서 필터를 사용 합니다.
    * 원본 열의 하위 집합에 대해 계산을 수행 하려면 플러그 인을 호출 하기 전에 해당 열만 프로젝션 합니다.
* `hint.distribution = per_node`스크립트의 논리를 배포할 때마다를 사용 합니다.
    * 또한 [partition 연산자](partitionoperator.md) 를 사용 하 여 입력 데이터 집합을 분할할 수 있습니다.
* 가능 하면 Kusto의 쿼리 언어를 사용 하 여 Python 스크립트의 논리를 구현 합니다.

    ## <a name="example"></a>예제

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

* 에서 Python 스크립트를 포함 하는 여러 줄 문자열을 생성 하려면 즐겨 사용 하는 `Kusto.Explorer` python 편집기 (*Jupyter*, *Visual Studio Code*, *PyCharm*등)에서 python 스크립트를 복사 합니다. 
  이제 다음 중 하나를 수행 합니다.
    * **F2** 키를 눌러 *Python에서 편집* 창을 엽니다. 이 창에 스크립트를 붙여 넣습니다. **확인**을 선택합니다. 스크립트는 따옴표와 새 줄로 데코 레이트 되므로 Kusto에서 유효 하 고 쿼리 탭에 자동으로 붙여 넣습니다.
    * Python 코드를 쿼리 탭에 직접 붙여 넣습니다. 해당 줄을 선택 하 고 **ctrl + K**, **ctrl + S** 바로 가기 키를 눌러 위와 같이 데코 레이트 합니다. 반대로 하려면 **ctrl + K**, **ctrl + M** 바로 가기 키를 누릅니다. [쿼리 편집기 바로 가기](../tools/kusto-explorer-shortcuts.md#query-editor)의 전체 목록을 참조 하세요.
* Kusto 문자열 구분 기호와 Python 문자열 리터럴 사이의 충돌을 방지 하려면 다음을 사용 합니다.
     * `'`Kusto 쿼리의 kusto 문자열 리터럴에 대 한 작은따옴표 ()
     * Python `"` 스크립트의 python 문자열 리터럴에 대 한 큰따옴표 문자 ()
* [ `externaldata` 연산자](externaldata-operator.md) 를 사용 하 여 Azure Blob storage와 같은 외부 위치에 저장 한 스크립트의 콘텐츠를 가져올 수 있습니다.
  
    ## <a name="example"></a>예제

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

## <a name="install-packages-for-the-python-plugin"></a>Python 플러그인 용 패키지 설치

다음과 같은 이유로 패키지를 직접 설치 해야 할 수도 있습니다.

* 패키지는 전용 패키지입니다.
* 패키지는 공용 이지만 플러그 인의 기본 이미지에는 포함 되어 있지 않습니다.

다음과 같이 패키지를 설치 합니다.

### <a name="prerequisites"></a>필수 구성 요소

  1. 패키지를 호스트 하는 blob 컨테이너를 만듭니다. 특히 클러스터와 동일한 장소에 있습니다. 예를 들어, `https://artifcatswestus.blob.core.windows.net/python` 클러스터가 미국 서 부에 있다고 가정 합니다.
  1. 클러스터의 [콜아웃 정책을](../management/calloutpolicy.md) 변경 하 여 해당 위치에 대 한 액세스를 허용 합니다.
        * 이렇게 변경 하려면 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요 합니다.

        * 예를 들어에 있는 blob에 대 한 액세스를 사용 하도록 설정 하려면 `https://artifcatswestus.blob.core.windows.net/python` 다음 명령을 실행 합니다.

        ```kusto
        .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
        ```

### <a name="install-packages"></a>패키지 설치

1. [Pypi](https://pypi.org/) 또는 기타 채널의 공용 패키지의 경우 패키지 및 해당 종속성을 다운로드 합니다.

   * `*.whl`필요한 경우 휠 () 파일을 컴파일합니다.
   * 로컬 Python 환경의 cmd 창에서 다음을 실행 합니다.
    
    ```python
    pip wheel [-w download-dir] package-name.
    ```

1. 필요한 패키지와 해당 종속성을 포함 하는 zip 파일을 만듭니다.

    * 개인 패키지의 경우: zip 패키지의 폴더 및 해당 종속성의 폴더입니다.
    * 공용 패키지의 경우 이전 단계에서 다운로드 한 파일을 압축 합니다.
    
    > [!NOTE]
    > * `.whl`부모 폴더가 아니라 파일 자체를 압축 해야 합니다.
    > * `.whl`기본 샌드박스 이미지에서 동일한 버전을 사용 하 여 이미 존재 하는 패키지에 대 한 파일을 건너뛸 수 있습니다.

1. 압축 된 파일을 아티팩트 위치의 blob에 업로드 합니다 (1 단계).

1. `python`플러그 인을 호출 합니다.
    * `external_artifacts`이름 및 zip 파일 (BLOB URL)에 대 한 참조의 속성 모음을 사용 하 여 매개 변수를 지정 합니다.
    * 인라인 python 코드에서를 가져오고 `Zipackage` `sandbox_utils` `install()` zip 파일의 이름을 사용 하 여 해당 메서드를 호출 합니다.

### <a name="example"></a>예제

가짜 데이터를 생성 하는 [Faker](https://pypi.org/project/Faker/) 패키지를 설치 합니다.

```kusto
range ID from 1 to 3 step 1 
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

| ID | 속성         |
|----|--------------|
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
