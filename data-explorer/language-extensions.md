---
title: Azure 데이터 탐색기 클러스터에서 언어 확장을 관리합니다.
description: 언어 확장을 사용하여 Azure 데이터 탐색기 KQL 쿼리 내에서 다른 언어를 통합합니다.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 52855651daccfe3c9baf5bb059530bcf7bfb0f19
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81502531"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Azure 데이터 탐색기 클러스터에서 언어 확장 관리(미리 보기)

언어 확장 기능을 사용하면 언어 확장 플러그인을 사용하여 다른 언어를 Azure Data 탐색기 KQL 쿼리에 통합할 수 있습니다. 관련 스크립트를 사용하여 UDF(사용자 정의 함수)를 실행하면 스크립트가 입력으로 테이블 형식 데이터를 가져옵니다. 플러그인의 런타임은 클러스터의 노드에서 실행되는 격리되고 안전한 환경인 [샌드박스에서](kusto/concepts/sandboxes.md)호스팅됩니다. 이 문서에서는 Azure 포털 내의 Azure Data Explorer 클러스터에서 언어 확장 플러그인을 관리합니다.

> [!NOTE]
> 현재 지원되는 Azure 데이터 탐색기 언어 확장은 파이썬 및 R입니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.

## <a name="enable-language-extensions-on-your-cluster"></a>클러스터에서 언어 확장 사용

> [!WARNING]
> 언어 확장을 활성화하기 전에 [제한 사항을](#limitations) 검토하십시오.

클러스터에서 언어 확장을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정에서** **구성을 선택합니다.** 
1. **구성** 창에서 언어 확장을 사용하려면 **켜기를** 선택합니다.
1. **저장**을 선택합니다.
 
    ![언어 확장](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> 언어 확장 프로세스를 사용하도록 설정하는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 동안 클러스터가 일시 중단됩니다.
 
## <a name="run-language-extension-integrated-queries"></a>언어 확장 통합 쿼리 실행

* [파이썬 통합 KQL 쿼리를 실행하는](kusto/query/pythonplugin.md)방법에 대해 알아봅니다.
* [R 통합 KQL 쿼리를 실행하는](kusto/query/rplugin.md)방법에 대해 알아봅니다. 

## <a name="disable-language-extensions-on-your-cluster"></a>클러스터에서 언어 확장 비활성화

> [!NOTE]
> 언어 확장을 사용하지 않도록 설정하면 몇 분 정도 걸릴 수 있습니다.

클러스터에서 언어 확장을 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정에서** **구성을 선택합니다.** 
1. **구성** 창에서 **끄기를** 선택하여 언어 확장을 사용하지 않도록 설정합니다.
1. **저장**을 선택합니다.

    ![언어 확장 끄기](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>제한 사항

* 언어 확장 기능은 [디스크 암호화를](manage-cluster-security.md)지원하지 않습니다. 
* 언어 확장 런타임 샌드박스는 관련 언어의 범위에서 쿼리가 실행되지 않더라도 디스크 공간을 할당합니다.
자세한 제한 사항은 [샌드박스를](kusto/concepts/sandboxes.md) 참조하십시오.
