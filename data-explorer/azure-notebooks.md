---
title: Azure 전자 필기장을 사용하여 Azure 데이터 탐색기의 데이터를 분석합니다.
description: 이 항목에서는 Azure 전자 필기장을 사용하여 쿼리를 만드는 방법을 보여 주며
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496265"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Azure 전자 필기장을 사용하여 Azure 데이터 탐색기의 데이터를 분석합니다.

[Azure 노트북은](https://notebooks.azure.com/) [Jupyter 노트북을](https://jupyter.org/)만들고 공유하는 것을 간소화하는 Azure 클라우드 서비스입니다. Azure 노트북을 사용하면 설명서, 코드 및 코드 실행 결과를 쉽게 결합할 수 있습니다.

> [!Note]
> * 다음 절차에서는 Azure 전자 필기장 환경에서 Python 클라이언트를 사용하여 Azure 데이터 탐색기를 쿼리합니다. 그러나 [KQLmagic을](https://docs.microsoft.com/azure/data-explorer/kqlmagic) 사용하여 Azure 데이터 탐색기를 쿼리할 수도 있습니다.
> * 일부 사용자는 Edge를 사용하여 인증하는 문제를 보고했습니다. 이러한 문제가 해결될 때까지 다른 브라우저를 사용하십시오.

## <a name="create-a-project"></a>프로젝트 만들기

1. 브라우저에서 [Azure 전자 필기장을](https://notebooks.azure.com/) 열고 로그인합니다.

1. 헤더에서 **내 프로젝트** 탭을 선택합니다. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. **+ 새 프로젝트를**선택합니다.
    
1. 새 프로젝트 만들기 대화 상자에서 다음을 **수행합니다.**
    1. 프로젝트 **이름을**입력합니다.
    1. **공개** 확인란을 선택 취소합니다.
        >[!Important]
        > 공개 확인란을 지우지 않으면 프로젝트가 열린 인터넷에 노출됩니다.
    1. **만들기**를 선택합니다.
    
    ![새 프로젝트 만들기](media/azurenotebooks/an-create-new-project-blank.png)

    프로젝트 ID가 자동으로 생성됩니다.

## <a name="create-a-notebook"></a>Notebook 만들기

1. 새 프로젝트에서 전자 필기장을 만듭니다. 전자 필기장에서 [지원되는 언어를](https://github.com/Azure/azure-kusto-python#minimum-requirements)사용해야 합니다.
전자 필기장을 만들려면 **+ 새** 전자 필기장을 선택하고 **전자 필기장을**선택합니다.

    ![새 전자 필기장 만들기](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. 새 **전자 필기전자 만들기** 대화 상자에서 전자 필기장 이름을 입력합니다.

1. **파이썬 3.6을** 선택하고 **새**.
    
    ![새 전자 필기장 만들기](media/azurenotebooks/an-create-new-notebook.png) 
    
1. 프로젝트에서 새 전자 필기장을 선택합니다.

    ![새 노트북 선택](media/azurenotebooks/an-select-notebook.png)

    파이썬 커널이 초기화 될 때까지 기다립니다. 채워진 원 아이콘이 빈 원 아이콘으로 변경되면 계속 진행할 수 있습니다.

    ![커널 초기화](media/azurenotebooks/an-python-init-icon.png)

1. 시스템 모듈을 가져오려면 다음 명령을 입력하고 **실행을**선택합니다.
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > 셀을 실행하려면 Shift+Enter를 누를 수도 있습니다.

1.  SDK 클래스를 가져오려면 다음 명령을 입력하고 **실행을**선택합니다.
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  연결 문자열을 빌드하고 Kusto 클라이언트를 시작하려면 다음 명령을 입력하고 **실행을**선택합니다.  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. 프롬프트에서 새 브라우저 탭을 엽니다. [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 
   
1. 권한 부여 코드를 입력하고 AAD()@microsoft.com계정에 로그인합니다. Kusto 클라이언트는 `kc` 이제 사용자의 ID를 사용하여 Azure 데이터 탐색기로 인증할 수 있습니다.

1. 전자 필기장으로 돌아가인증 결과를 확인합니다. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Kusto 쿼리 실행

1. Kusto 쿼리를 입력하고 **실행을 선택합니다.** 다음은 그 예입니다.

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [쿠스토 파이썬 GitHub 리포지토리](https://github.com/Azure/azure-kusto-python)
