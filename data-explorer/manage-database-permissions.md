---
title: Azure 데이터 탐색기에서 데이터베이스 사용 권한 관리
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 및 테이블에 대한 역할 기반 액세스 제어에 대해 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 0ff2b0892231f52390f103fe299f219a7a3f3860
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942049"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Azure 데이터 탐색기 데이터베이스 권한 관리

Azure 데이터 탐색기를 사용하면 *역할 기반 액세스 제어* 모델을 사용하여 데이터베이스 및 테이블에 대한 액세스를 제어할 수 있습니다. 이 모델에서 *주체*(사용자, 그룹 및 앱)는 *역할*에 매핑됩니다. 주체는 자신에게 할당된 역할에 따라 리소스에 액세스할 수 있습니다.

이 문서에서는 사용 가능한 역할에 대해 설명하고 Azure Portal 및 Azure Data Explorer 관리 명령을 사용하여 이러한 역할에 보안 주체를 할당하는 방법을 설명합니다.

## <a name="roles-and-permissions"></a>역할 및 권한

Azure 데이터 탐색기에는 다음 역할이 있습니다.

|역할                       |사용 권한                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|데이터베이스 관리자             |특정 데이터베이스의 범위에서 모든 작업을 수행할 수 있습니다.|
|데이터베이스 사용자              |데이터베이스의 모든 데이터 및 메타데이터를 읽을 수 있습니다. 또한 데이터베이스에서 테이블(해당 테이블의 테이블 관리자가 됨) 및 함수를 만들 수 있습니다.|
|데이터베이스 보기 권한자            |데이터베이스의 모든 데이터 및 메타데이터를 읽을 수 있습니다.|
|데이터베이스 수집 권한자          |데이터베이스의 모든 기존 테이블에 데이터를 수집할 수 있지만 데이터를 쿼리할 수 없습니다.|
|데이터베이스 모니터           |데이터베이스 및 해당 자식 엔터티의 컨텍스트에서 '.show ...' 명령을 실행할 수 있습니다.|
|테이블 관리자                |특정 테이블의 범위에서 모든 작업을 수행할 수 있습니다. |
|테이블 수집 권한자             |특정 테이블의 범위에서 데이터를 수집할 수 있지만 데이터를 쿼리할 수 없습니다.|

## <a name="manage-permissions-in-the-azure-portal"></a>Azure Portal에서 권한 관리

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure 데이터 탐색기 클러스터로 이동합니다.

1. **개요** 섹션에서 권한을 관리할 데이터베이스를 선택합니다.

    ![데이터베이스 선택](media/manage-database-permissions/select-database.png)

1. **권한**을 선택한 다음, **추가**를 선택합니다.

    ![데이터베이스 사용 권한](media/manage-database-permissions/database-permissions.png)

1. **데이터베이스 권한 추가** 아래에서 주체를 할당할 역할을 선택한 후 **주체 선택**을 선택합니다.

    ![데이터베이스 권한 추가](media/manage-database-permissions/add-permission.png)

1. 주체를 조회하여 선택하고 **선택**을 선택합니다.

    :::image type="content" source="media/manage-database-permissions/new-principals.png" alt-text="새 보안 주체 페이지 Azure Portal의 스크린샷 사용자 이름 및 이미지가 선택 되 고 강조 표시 됩니다. 선택 단추도 강조 표시 됩니다." border="false":::

1. **저장**을 선택합니다.

    :::image type="content" source="media/manage-database-permissions/save-permission.png" alt-text="새 보안 주체 페이지 Azure Portal의 스크린샷 사용자 이름 및 이미지가 선택 되 고 강조 표시 됩니다. 선택 단추도 강조 표시 됩니다." border="false":::

## <a name="manage-permissions-with-management-commands"></a>관리 명령으로 권한 관리

1. 에 로그인 하 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 고 클러스터를 아직 사용할 수 없는 경우 추가 합니다.

1. 왼쪽 창에서 적절한 데이터베이스를 선택합니다.

1. `.add` 명령을 사용하여 역할에 주체를 할당합니다. `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')` 데이터베이스 사용자 역할에 사용자를 추가하려면 다음 명령을 실행하여 데이터베이스 이름 및 사용자를 대체합니다.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    명령의 출력은 데이터베이스에서 기존 사용자 및 해당 사용자가 할당된 역할의 목록을 표시합니다.
    
    Azure Active Directory 및 Kusto 권한 부여 모델과 관련 된 예는 [원칙 및 Id 공급자](kusto/management/access-control/principals-and-identity-providers.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[쿼리 작성](write-queries.md)
