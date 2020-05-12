---
title: 데이터 제거를 사용 하 여 Azure 데이터 탐색기의 장치 또는 서비스에서 개인 데이터 삭제
description: 데이터 제거를 사용 하 여 Azure 데이터 탐색기에서 데이터를 제거 (삭제) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 3f170a48f31f9d842e39fcf42fcfce0c383c71ed
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83232387"
---
# <a name="enable-data-purge-on-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에서 데이터 제거 사용

[!INCLUDE [gdpr-intro-sentence](includes/gdpr-intro-sentence.md)]

Azure 데이터 탐색기는 개별 레코드를 삭제할 수 있는 기능을 지원 합니다. 명령을 통한 데이터 삭제 `.purge` 는 개인 데이터를 보호 하며 다른 시나리오에서는 사용할 수 없습니다. 자주 삭제 요청을 지원 하거나 대량의 데이터를 삭제 하는 것이 아니라 서비스에 상당한 성능 영향을 줄 수 있습니다.

명령을 실행 하면 `.purge` 완료 하는 데 몇 일이 걸릴 수 있는 프로세스가 트리거됩니다. 이 적용 된 레코드의 "밀도"가 `predicate` 크면 프로세스가 테이블의 모든 데이터를 다시 수집 합니다. 이 프로세스는 성능 및 COGS에 상당한 영향을 미칩니다. 자세한 내용은 [Azure 데이터 탐색기의 데이터 제거](kusto/concepts/data-purge.md)를 참조 하세요.

## <a name="methods-of-invoking-purge-operations"></a>제거 작업을 호출 하는 방법 

Azure 데이터 탐색기 (Kusto)는 개별 레코드 삭제를 모두 지원 하 고 전체 테이블을 제거 합니다. `.purge`다른 사용 시나리오에 대 한 [두 가지 방법으로 명령을 호출할](kusto/concepts/data-purge.md#purge-table-tablename-records-command) 수 있습니다.

* 프로그래밍 방식 호출: 응용 프로그램에서 호출 하는 단일 단계입니다. 이 명령을 호출 하면 제거 실행 시퀀스가 직접 트리거됩니다.

* 인간 호출: 별도의 단계로 명시적 확인이 필요한 2 단계 프로세스입니다. 명령을 호출 하면 실제 제거를 실행 하기 위해 제공 해야 하는 확인 토큰이 반환 됩니다. 이 프로세스를 통해 잘못 된 데이터를 실수로 삭제 하는 위험을 줄일 가능성이 줄어듭니다. 이 옵션을 사용 하면 상당한 콜드 캐시 데이터가 있는 큰 테이블에서 완료 하는 데 시간이 오래 걸릴 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [웹 UI](https://dataexplorer.azure.com/)에 로그인 합니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md) 만들기

## <a name="enable-data-purge-on-your-cluster"></a>클러스터에서 데이터 제거 사용

> [!WARNING]
> * 데이터 제거를 사용 하도록 설정 하려면 서비스를 다시 시작 해야 쿼리를 삭제할 수 있습니다.
> * 데이터 제거를 사용 하도록 설정 하기 전에 [제한 사항을](#limitations) 검토 하세요.

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 **켜기** 를 선택 하 여 **제거 사용**을 설정 합니다.
1. **저장**을 선택합니다.
 
    ![제거 사용](media/data-purge-portal/enable-purge-on.png)

## <a name="disable-data-purge-on-your-cluster"></a>클러스터에서 데이터 제거 사용 안 함

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 **해제** 를 선택 하 여 **제거 사용**을 사용 하지 않도록 설정 합니다.
1. **저장**을 선택합니다.

    ![제거 사용](media/data-purge-portal/enable-purge-off.png)

## <a name="limitations"></a>제한 사항

* 제거 프로세스는 마지막 이며 취소할 수 없습니다. 이 프로세스를 "실행 취소" 하거나 제거 된 데이터를 복구할 수 없습니다. 따라서 [실행 취소 테이블 drop](kusto/management/undo-drop-table-command.md) 과 같은 명령은 삭제 된 데이터를 복구할 수 없으며, 이전 버전으로 데이터를 롤백하는 것은 최신 제거를 "이전"으로 이동할 수 없습니다.
* `.purge`명령은 데이터 관리 끝점에 대해 실행 됩니다. * https://ingest- [해당 clustername]. [ Region] .kusto. net*. 이 명령에는 관련 데이터베이스에 대 한 [데이터베이스 관리자](kusto/management/access-control/role-based-authorization.md) 권한이 필요 합니다. 
* 제거 프로세스 성능에 영향을 줄 수 있으므로 호출자는 제거 프로세스의 심각한 COGS 영향을 줄이기 위해 최소한의 테이블에 관련 데이터가 포함 되도록 데이터 스키마를 수정 하 고 테이블당 일괄 처리 명령을 수정 해야 합니다.
* 제거 `predicate` 명령의 매개 변수는 제거할 레코드를 지정 하는 데 사용 됩니다. `Predicate`크기는 63 KB로 제한 됩니다. 

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 제거](kusto/concepts/data-purge.md)
