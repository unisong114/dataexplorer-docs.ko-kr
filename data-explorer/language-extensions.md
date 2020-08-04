---
title: Azure 데이터 탐색기 클러스터에서 언어 확장을 관리 합니다.
description: 언어 확장을 사용 하 여 Azure 데이터 탐색기 KQL 쿼리 내에서 다른 언어를 통합 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 104b3e4db18334f33c54177da7b996bc679db2de
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515892"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Azure 데이터 탐색기 클러스터에서 언어 확장 관리 (미리 보기)

언어 확장 기능을 사용 하면 언어 확장 플러그 인을 사용 하 여 다른 언어를 Azure 데이터 탐색기 KQL 쿼리에 통합할 수 있습니다. 관련 스크립트를 사용 하 여 UDF (사용자 정의 함수)를 실행 하면 스크립트에서 테이블 형식 데이터를 입력으로 가져오며 테이블 형식 출력을 생성 해야 합니다. 플러그 인의 런타임은 클러스터의 노드에서 실행 되는 격리 된 보안 환경인 [샌드박스에서](kusto/concepts/sandboxes.md)호스팅됩니다. 이 문서에서는 Azure Portal 내의 Azure 데이터 탐색기 클러스터에서 언어 확장 플러그 인을 관리 합니다.

> [!NOTE]
> 현재 지원 되는 Azure 데이터 탐색기 언어 확장은 Python 및 R입니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.

## <a name="enable-language-extensions-on-your-cluster"></a>클러스터에서 언어 확장 사용

> [!WARNING]
> 언어 확장을 사용 하도록 설정 하기 전에 [제한 사항을](#limitations) 검토 하세요.

클러스터에서 언어 확장을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 설정 **을 선택 하 여 언어** 확장을 사용 하도록 설정 합니다.
1. **저장**을 선택합니다.
 
    ![언어 확장](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> 언어 확장 프로세스를 사용 하도록 설정 하는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 동안에는 클러스터가 일시 중단 됩니다.
 
## <a name="run-language-extension-integrated-queries"></a>실행 언어 확장 통합 쿼리

* [Python 통합 KQL 쿼리를 실행](kusto/query/pythonplugin.md)하는 방법에 대해 알아봅니다.
* [R INTEGRATED KQL 쿼리를 실행](kusto/query/rplugin.md)하는 방법에 대해 알아봅니다. 

## <a name="disable-language-extensions-on-your-cluster"></a>클러스터에서 언어 확장 사용 안 함

> [!NOTE]
> 언어 확장을 사용 하지 않도록 설정 하는 데 몇 분 정도 걸릴 수 있습니다.

클러스터에서 언어 확장을 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 **해제** 를 선택 하 여 언어 확장을 사용 하지 않도록 설정 합니다.
1. **저장**을 선택합니다.

    ![언어 확장 해제](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>제한 사항

* 언어 확장 기능은 [디스크 암호화](cluster-disk-encryption.md)를 지원 하지 않습니다. 
* 언어 확장 런타임 샌드박스는 관련 언어의 범위에서 실행 되는 쿼리가 없는 경우에도 디스크 공간을 할당 합니다.
자세한 제한 사항은 [샌드박스](kusto/concepts/sandboxes.md) 를 참조 하세요.
