---
title: Azure CLI를 사용 하 여 고객 관리 키 구성
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure 데이터 탐색기의 데이터에서 고객이 관리 하는 키 암호화를 구성 하는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: astauben
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: bc8ce7a3d85a64a2a19e4194e7c9e6ab23b0fef6
ms.sourcegitcommit: a7e040fc844098323aa1c00e254bcbcd41fe587f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84426537"
---
# <a name="configure-customer-managed-keys-using-azure-cli"></a>Azure CLI를 사용 하 여 고객 관리 키 구성

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-azure-cli"></a>Azure CLI를 사용 하 여 고객이 관리 하는 키로 암호화 사용
이 문서에서는 Azure CLI 클라이언트를 사용 하 여 고객이 관리 하는 키 암호화를 사용 하도록 설정 하는 방법을 보여 줍니다. 기본적으로 Azure 데이터 탐색기 암호화는 Microsoft 관리 키를 사용 합니다. Azure 데이터 탐색기 클러스터가 고객이 관리 하는 키를 사용 하도록 구성 하 고 클러스터와 연결할 키를 지정 합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

1. 클러스터가 등록 된 구독을 설정 합니다. *MyAzureSub* 을 사용 하려는 Azure 구독의 이름으로 바꿉니다.

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

1. 다음 명령을 실행 하 여 새 키를 설정 합니다.
    ```azurecli-interactive
    az kusto cluster update --cluster-name "mytestcluster" --resource-group "mytestrg" --key-vault-properties key-name="<key-name>" key-version="<key-version>" key-vault-uri="<key-vault-uri>"
    ```
1. 다음 명령을 실행 하 고 ' keyVaultProperties ' 속성을 확인 하 여 클러스터가 성공적으로 업데이트 되었는지 확인 합니다.

    ```azurecli-interactive
    az kusto cluster show --cluster-name "mytestcluster" --resource-group "mytestrg"
    ```

