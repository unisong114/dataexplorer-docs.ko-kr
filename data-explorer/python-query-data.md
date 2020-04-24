---
title: Azure Data Explorer Python 라이브러리를 사용하여 데이터 쿼리
description: 이 문서에서는 파이썬을 사용하여 Azure 데이터 탐색기에서 데이터를 쿼리하는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ebd65f2dcbb0040b764290627bbfd2901aa9a7d3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501439"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Azure Data Explorer Python 라이브러리를 사용하여 데이터 쿼리

이 문서에서는 Azure 데이터 탐색기를 사용하여 데이터를 쿼리합니다. Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다.

Azure 데이터 탐색기는 [Python에 대한 데이터 클라이언트 라이브러리](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)를 제공합니다. 이 라이브러리를 사용하여 코드에서 데이터를 쿼리할 수 있습니다. 학습을 돕기 위해 설정한 *도움말 클러스터의* 테이블에 연결합니다. 해당 클러스터에서 테이블을 쿼리하고 결과를 반환할 수 있습니다.

이 문서는 [Azure 전자 필기장으로도](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb)사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [파이썬 3.4+](https://www.python.org/downloads/)

* AAD(Azure Active Directory)의 멤버인 조직 메일 계정

## <a name="install-the-data-library"></a>데이터 라이브러리 설치

*azure-kusto-data*를 설치합니다.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>import 문 및 상수 추가

라이브러리의 클래스 및 데이터 분석 라이브러리인 *pandas*를 가져옵니다.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

애플리케이션을 인증하기 위해 Azure 데이터 탐색기는 AAD 테넌트 ID를 사용합니다. 테넌트 ID를 찾으려면 다음 URL을 사용하여 *YourDomain*을 사용자 도메인으로 대체합니다.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

예를 들어 도메인이 *contoso.com*인 경우 URL은 [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)입니다. 결과를 보려면 이 URL을 클릭합니다. 첫 번째 줄은 다음과 같습니다.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

이 경우의 테넌트 ID는 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`입니다. 이 코드를 실행하기 전에 AAD_TENANT_ID에 대한 값을 설정합니다.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

이제 연결 문자열을 구성합니다. 이 예제에서는 디바이스 인증을 사용하여 클러스터에 액세스합니다. [AAD 응용 프로그램 인증서,](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24) [AAD 응용 프로그램 키](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)및 [AAD 사용자 및 암호를](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)사용할 수도 있습니다.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Azure 데이터 탐색기에 연결하고 쿼리 실행

클러스터에 대해 쿼리를 실행하고 출력을 데이터 프레임에 저장합니다. 이 코드가 실행되면 다음과 같은 메시지가 반환됩니다. *로그인하려면 웹 브라우저를 사용하여 https://microsoft.com/devicelogin 페이지를 열고 코드 F3W4VWZDM을 입력하여 인증하세요.* 단계에 따라 로그인한 후 돌아가서 다음 코드 블록을 실행합니다.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>DataFrame에서 데이터 탐색

로그인을 입력한 후 쿼리가 결과를 반환하고 결과는 데이터 프레임에 저장됩니다. 다른 데이터 프레임을 사용하는 것처럼 결과를 사용할 수 있습니다.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

StormEvents 테이블의 상위 10개 결과가 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 데이터 탐색기 파이썬 라이브러리를 사용하여 데이터 수집](python-ingest-data.md)
