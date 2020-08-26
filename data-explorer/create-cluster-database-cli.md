---
title: Azure CLI를 사용 하 여 Azure 데이터 탐색기 클러스터 & DB 만들기
description: Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법 알아보기
author: orspod
ms.author: orspodek
ms.reviewer: radennis
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: b15b2330df96071069e6b86a0fff6b9d77a6b0a1
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872831"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 템플릿](create-cluster-database-resource-manager.md)

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. 이 문서에서는 Azure CLI를 사용 하 여 클러스터와 데이터베이스를 만듭니다.

## <a name="prerequisites"></a>전제 조건

이 문서를 완료 하려면 Azure 구독이 필요 합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에서는 Azure CLI 버전 2.0.4 이상을 이상이 필요 합니다. `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="configure-the-cli-parameters"></a>CLI 매개 변수 구성

Azure Cloud Shell에서 명령을 실행하는 경우에는 다음 단계가 필요하지 않습니다. 로컬에서 CLI를 실행하는 경우 다음 단계에 따라 Azure에 로그인하고 현재 구독을 설정합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

1. 클러스터를 만들려는 구독을 설정합니다. `MyAzureSub`를 사용할 Azure 구독의 이름으로 바꿉니다.

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```
   
1. 최신 Kusto CLI 버전을 사용 하도록 확장을 설치 합니다.

    ```azurecli-interactive
    az extension add -n kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

1. 다음 명령을 사용하여 클러스터를 만듭니다.

    ```azurecli-interactive
    az kusto cluster create --cluster-name azureclitest --sku name="Standard_D13_v2" tier="Standard" --resource-group testrg --location westus
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | name | *azureclitest* | 원하는 클러스터 이름입니다.|
   | sku | *Standard_D13_v2* | 클러스터에 사용될 SKU입니다. Parameters: *name* -SKU 이름입니다. *계층* -SKU 계층입니다. |
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | 위치 | *westus* | 클러스터가 생성 될 위치입니다. |

    사용 가능한 선택적 매개 변수(예: 클러스터 용량)가 추가로 있습니다.

1. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```azurecli-interactive
    az kusto cluster show --cluster-name azureclitest --resource-group testrg
    ```

결과에 값이 `Succeeded`인 `provisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 명령을 사용하여 데이터베이스를 만듭니다.

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --database-name clidatabase --resource-group testrg --read-write-database soft-delete-period=P365D hot-cache-period=P31D location=westus
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | cluster-name | *azureclitest* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | 데이터베이스 이름 | *clidatabase* | 데이터베이스의 이름입니다.|
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | 읽기/쓰기-데이터베이스 | *P365D* *P31D* *westus* | 데이터베이스 유형입니다. 매개 변수: *일시 삭제-기간* -데이터를 쿼리에 사용할 수 있는 시간을 나타냅니다. 자세한 내용은 [보존 정책](kusto/management/retentionpolicy.md)을 참조하세요. *핫 캐시-기간* -데이터를 캐시에 보관 하는 시간을 나타냅니다. 자세한 내용은 [캐시 정책](kusto/management/cachepolicy.md)을 참조하세요. *location* -데이터베이스가 생성 될 위치입니다. |

1. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```azurecli-interactive
    az kusto database show --database-name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 문서를 따르려면 만든 리소스를 유지 합니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```azurecli-interactive
    az kusto cluster delete --cluster-name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 Python 라이브러리를 사용 하 여 데이터 수집](python-ingest-data.md)
