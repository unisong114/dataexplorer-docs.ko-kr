---
title: VS 코드를 사용하여 인라인 파이썬 - Azure 데이터 탐색기
description: VS 코드를 사용하여 Kusto 쿼리 언어(KQL) 인라인 파이썬을 디버깅하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 808d51ff5bc070449c87d14822fe4f41d2038950
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499099"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>VS 코드를 사용하여 Kusto 쿼리 언어 인라인 파이썬 디버그

Azure Data Explorer는 [파이썬() 플러그인을](kusto/query/pythonplugin.md)사용하여 Kusto 쿼리 언어에 포함된 파이썬 코드 실행을 지원합니다. 플러그인 런타임은 격리되고 안전한 파이썬 환경인 샌드박스에서 호스팅됩니다. python() 플러그인 기능은 OSS 파이썬 패키지의 거대한 아카이브와 Kusto 쿼리 언어 기본 기능을 확장합니다. 이 확장을 사용하면 쿼리의 일부로 기계 학습, 인공 지능, 통계 및 타임계와 같은 고급 알고리즘을 실행할 수 있습니다.

Kusto 쿼리 언어 도구는 Python 알고리즘을 개발하고 디버깅하는 데 적합하지 않습니다. 따라서 Jupyter, PyCharm, VS 또는 VS 코드와 같은 좋아하는 파이썬 통합 개발 환경에서 알고리즘을 개발하십시오. 알고리즘이 완료되면 복사하여 KQL에 붙여넣습니다. 이 워크플로를 개선하고 간소화하기 위해 Azure Data Explorer는 Kusto Explorer 또는 웹 UI 클라이언트와 KQL 인라인 파이썬 코드를 작성하고 디버깅하기 위한 VS 코드 간의 통합을 지원합니다. 

> [!NOTE]
> 이 워크플로는 상대적으로 작은 입력 테이블(최대 몇 MB)을 디버깅하는 데만 사용할 수 있습니다. 따라서 디버깅에 대 한 입력을 제한 해야 할 수 있습니다.  큰 테이블을 처리해야 하는 경우 을 `| take` `| sample`사용하여 디버깅할 `where rand() < 0.x`수 있도록 제한합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. 파이썬 [아나콘다 배포판을](https://www.anaconda.com/distribution/#download-section)설치합니다. **고급 옵션에서** **내 PATH 환경 변수에 아나콘다 추가를**선택합니다.
2. [비주얼 스튜디오 코드](https://code.visualstudio.com/Download) 설치
3. [비주얼 스튜디오 코드에 대한 파이썬 확장을 설치합니다.](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="run-your-query-in-your-client-application"></a>클라이언트 응용 프로그램에서 쿼리 실행

1. 클라이언트 응용 프로그램에서 인라인 파이썬이 포함된 쿼리를`set query_python_debug;`
1. 쿼리를 실행합니다.
    * Kusto 탐색기: VS 코드는 *debug_python.py* 스크립트와 함께 자동으로 시작 됩니다.
    * 쿠스토 웹 UI: 
        1. 다운로드 및 *debug_python.py,* *df.txt,* 및 *kargs.txt*. 창에서 **을 선택합니다.** 선택한 디렉터리에서 파일을 **저장합니다.** 

            ![웹 UI는 인라인 파이썬 파일을 다운로드합니다.](media/debug-inline-python/webui-inline-python.png)

        1. *debug_python.py를* 마우스 오른쪽 버튼으로 클릭하고 VS 코드로 엽니다. 
        *debug_python.py* 스크립트에는 *df.txt의* 입력 데이터 프레임과 *kargs.txt의*매개 변수 사전을 초기화하기 위해 템플릿 코드에 의해 접두사에 있는 KQL 쿼리의 인라인 파이썬 코드가 포함되어 있습니다.    
            
1. VS 코드에서 VS 코드 디버거: **디버그** > **시작 디버깅(F5)을**시작하고 **파이썬** 구성을 선택합니다. 디버거가 시작되고 자동으로 중단점을 사용하여 인라인 코드를 디버깅합니다.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS 코드에서 인라인 파이썬 디버깅은 어떻게 작동합니까?

1. 필요한 `| evaluate python()` 절에 도달할 때까지 쿼리가 구문 분석되고 서버에서 실행됩니다.
1. Python 샌드박스가 호출되지만 코드를 실행하는 대신 입력 테이블, 매개 변수 사전 및 코드를 직렬화하여 클라이언트로 다시 보냅니다.
1. 이 세 개체는 *df.txt,* *kargs.txt*및 선택된 디렉터리(웹 UI) 또는 클라이언트 %TEMP% 디렉토리(Kusto Explorer)의 *debug_python.py의* 세 파일에 저장됩니다.
1. VS 코드가 시작되고 각 파일에서 df 및 kargs를 초기화하는 접두사 코드가 포함된 *debug_python.py* 파일이 미리 로드되고 KQL 쿼리에 포함된 파이썬 스크립트가 표시됩니다.

## <a name="query-example"></a>쿼리 예

1. 클라이언트 응용 프로그램에서 다음 KQL 쿼리를 실행합니다.

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    결과 표를 참조하십시오.

    | x  | x 4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. 다음을 사용하여 `set query_python_debug;`클라이언트 응용 프로그램에서 동일한 KQL 쿼리를 실행합니다.

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS 코드가 시작됩니다.

    ![VS 코드 시작](media/debug-inline-python/launch-vs-code.png)

1. VS 코드 디버그 콘솔에서 '결과' 데이터 프레임을 디버깅하고 인쇄합니다.

    ![VS 코드 디버그](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python 샌드박스 이미지와 로컬 설치 사이에 차이가 있을 수 있습니다. [플러그인을 쿼리하여 특정 패키지에 대한 샌드박스 이미지를 확인합니다.](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)
