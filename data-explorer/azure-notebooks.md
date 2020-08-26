---
title: Azure Notebooks를 사용 하 여 Azure 데이터 탐색기에서 데이터 분석
description: 이 항목에서는 Azure 노트북을 사용 하 여 쿼리를 만드는 방법을 보여 줍니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: d41c9e2719ef5139d89986ff5333c03e6e34d26c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872474"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Azure Notebooks를 사용 하 여 Azure 데이터 탐색기에서 데이터 분석

[Azure Notebooks](https://notebooks.azure.com/) 은 [Jupyter 노트북](https://jupyter.org/)만들기 및 공유를 간소화 하는 Azure 클라우드 서비스입니다. Azure Notebooks를 사용 하면 문서, 코드 및 코드 실행 결과를 쉽게 결합할 수 있습니다.

> [!Note]
> * 다음 절차에서는 Azure Notebooks 환경의 Python 클라이언트를 사용 하 여 Azure 데이터 탐색기를 쿼리 합니다. 그러나 [KQLmagic](kqlmagic.md) 을 사용 하 여 Azure 데이터 탐색기를 쿼리할 수도 있습니다.
> * 일부 사용자가 Edge를 사용 하 여 인증 하는 문제를 보고 했습니다. 이러한 문제가 해결 될 때까지 다른 브라우저를 사용 합니다.

## <a name="create-a-project"></a>프로젝트 만들기

1. 브라우저에서 [Azure Notebooks](https://notebooks.azure.com/) 을 열고 로그인 합니다.

1. 머리글에서 **내 프로젝트** 탭을 선택 합니다. 

    :::image type="content" source="media/azurenotebooks/an-myprojects.png" alt-text="프로젝트 페이지, 내 프로젝트 탭, Microsoft Azure Notebooks, Azure 데이터 탐색기" lightbox="media/azurenotebooks/an-myprojects.png#lightbox":::

1. **+ 새 프로젝트**를 선택 합니다.
    
1. **새 프로젝트 만들기** 대화 상자에서 다음을 수행 합니다.
    1. **프로젝트 이름을**입력 합니다.
    1. **공용** 확인란의 선택을 취소 합니다.
        >[!Important]
        > 공용 확인란의 선택을 취소 하지 않으면 프로젝트가 오픈 인터넷에 노출 됩니다.
    1. **만들기**를 선택합니다.
    
    ![새 프로젝트 만들기](media/azurenotebooks/an-create-new-project-blank.png)

    프로젝트 ID가 자동으로 생성 됩니다.

## <a name="create-a-notebook"></a>Notebook 만들기

1. 새 프로젝트에서 노트북을 만듭니다. 노트북은 [지원 되는 언어](https://github.com/Azure/azure-kusto-python#minimum-requirements)를 사용 해야 합니다.
노트북을 만들려면 **+ 새로 만들기** 를 선택 하 고 **노트북**을 선택 합니다.

    ![새 노트북 만들기](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. **새 노트북 만들기** 대화 상자에서 노트북 이름을 입력 합니다.

1. **Python 3.6** 을 선택 하 고 **새로 만들기**를 선택 합니다.
    
    ![새 노트북 만들기](media/azurenotebooks/an-create-new-notebook.png) 
    
1. 프로젝트에서 새 노트북을 선택 합니다.

    ![새 노트북 선택](media/azurenotebooks/an-select-notebook.png)

    Python 커널이 초기화 될 때까지 기다립니다. 채워진 원 아이콘이 빈 원 아이콘으로 변경 되 면 계속 진행할 수 있습니다.

    ![커널 초기화](media/azurenotebooks/an-python-init-icon.png)

1. 시스템 모듈을 가져오려면 다음 명령을 입력 하 고 **실행**을 선택 합니다.
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > 셀을 실행 하려면 Shift + Enter를 누를 수도 있습니다.

1.  SDK 클래스를 가져오려면 다음 명령을 입력 하 고 **실행**을 선택 합니다.
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  연결 문자열을 작성 하 고 Kusto client를 시작 하려면 다음 명령을 입력 하 고 **실행**을 선택 합니다.  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. 프롬프트에서 새 브라우저 탭을 엽니다 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) . 
   
1. 인증 코드를 입력 하 고 AAD () 계정에 로그인 @microsoft.com 합니다. 이제 Kusto 클라이언트는 `kc` id를 사용 하 여 Azure 데이터 탐색기에 인증할 수 있습니다.

1. 노트북으로 돌아와서 인증 결과를 확인 합니다. 

:::image type="content" source="media/azurenotebooks/an-python-commands.png" alt-text="인증 결과 출력, 노트북 창, Microsoft Azure Notebooks, Azure 데이터 탐색기" lightbox="media/azurenotebooks/an-python-commands.png#lightbox":::

## <a name="execute-a-kusto-query"></a>Kusto 쿼리 실행

1. Kusto 쿼리를 입력 하 고 **실행**을 선택 합니다. 예를 들면

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

:::image type="content" source="media/azurenotebooks/an-commands.png" alt-text="실행 단추, 노트북 창, Microsoft Azure Notebooks, Azure 데이터 탐색기" lightbox="media/azurenotebooks/an-commands.png#lightbox":::

## <a name="next-steps"></a>다음 단계

* [Kusto-python GitHub 리포지토리](https://github.com/Azure/azure-kusto-python)
