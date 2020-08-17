---
title: Azure 데이터 공유를 사용 하 여 Azure 데이터 탐색기와 데이터 공유
description: Azure 데이터 탐색기 및 Azure 데이터 공유와 데이터를 공유 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 29d3c10dc08d0506f43af7127cbf705a8b1881c1
ms.sourcegitcommit: ec191391f5ea6df8c591e6d747c67b2c46f98ac4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259914"
---
# <a name="use-azure-data-share-to-share-data-with-azure-data-explorer"></a>Azure 데이터 공유를 사용 하 여 Azure 데이터 탐색기와 데이터 공유

파일 공유, FTP, 전자 메일 및 Api를 통해 데이터를 공유 하는 일반적인 방법에는 여러 가지가 있습니다. 이러한 메서드를 통해 팀과 조직 간에 데이터를 이동 하는 데이터 파이프라인을 작성 하 고 유지 관리 해야 합니다. Azure 데이터 탐색기을 사용 하면 회사 또는 외부 파트너의 사용자와 쉽고 안전 하 게 데이터를 공유할 수 있습니다. 공유는 데이터 파이프라인을 빌드하거나 유지 관리할 필요 없이 거의 실시간으로 발생 합니다. 공급자 측에서 스키마 및 데이터를 비롯 한 모든 데이터베이스 변경 내용은 소비자 쪽에서 즉시 사용할 수 있습니다.

[![Azure 금요일 비디오](https://img.youtube.com/vi/Q3MJv90PegE/0.jpg)](https://www.youtube.com/watch?v=Q3MJv90PegE?&autoplay=1)

Azure 데이터 탐색기는 고객이 동일한 기본 저장소에서 여러 계산 (읽기 전용) 인스턴스를 실행할 수 있도록 하는 저장소 및 계산을 분리 합니다. 데이터베이스를 원격 클러스터의 읽기 전용 데이터베이스인 [종동체 데이터베이스로](follower.md)연결할 수 있습니다.

## <a name="configure-data-sharing"></a>데이터 공유 구성 

다음 방법 중 하나를 사용 하 여 데이터 공유를 구성할 수 있습니다.

* [Azure 데이터 공유](/azure/data-share/) 를 사용 하 여 회사나 외부 파트너 및 고객과 초대 및 공유를 보내고 관리할 수 있습니다. Azure 데이터 공유는 [종동체 데이터베이스](follower.md) 를 사용 하 여 공급자와 소비자의 Azure 데이터 탐색기 클러스터 사이에 기호화 된 링크를 만듭니다. 이 옵션은 Azure 데이터 탐색기 클러스터 및 기타 데이터 서비스에서 모든 데이터 공유를 보고 관리할 수 있는 단일 창을 제공 합니다. Azure 데이터 공유를 사용 하 여 여러 Azure 활성 직접 테 넌 트의 조직 간에 데이터를 공유할 수도 있습니다.
* 보고 요구 사항에 맞게 규모를 확장 하는 추가 계산이 필요한 시나리오에 대해서만 [종동체 데이터베이스](follower.md) 를 직접 구성 합니다.

> [!Note] 
> 공유 관계가 설정 되 면 Azure 데이터 공유는 공급자와 소비자의 Azure 데이터 탐색기 클러스터 사이에 기호화 된 링크를 만듭니다. 데이터 공급자가 액세스를 취소 하면 기호화 된 링크가 삭제 되 고 공유 데이터베이스는 데이터 소비자에 게 더 이상 제공 되지 않습니다.

:::image type="content" source="media/data-share/adx-datashare-image.png" alt-text="Azure 데이터 탐색기 데이터 공유":::

데이터 공급자는 데이터베이스 수준 또는 클러스터 수준에서 데이터를 공유할 수 있습니다. 데이터베이스를 공유 하는 클러스터는 리더 클러스터 이며 공유를 받는 클러스터는 종동체 클러스터입니다. 종동체 클러스터는 하나 이상의 리더 클러스터 데이터베이스를 따를 수 있습니다. 종동체 클러스터는 정기적으로 동기화 하 여 변경 내용을 확인 합니다. 리더와 종동체 간의 지연 시간은 메타 데이터 및 데이터의 전체 크기에 따라 몇 초에서 몇 분까지 다릅니다. 데이터는 소비자 클러스터에 캐시 되며, 읽기 또는 쿼리 작업에만 사용할 수 있으며, 핫 캐싱 정책 및 데이터베이스 권한은 재정의 하지 않습니다. 종동체 클러스터에서 실행 되는 쿼리는 로컬 캐시를 사용 하 고 리더 클러스터의 리소스를 사용 하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
1. 리더 및 종동체 용 [공급자 클러스터 및 DB를 만듭니다](create-cluster-database-portal.md) .
1. [수집](ingest-sample-data.md) [개요](ingest-data-overview.md)에 설명 된 다양 한 방법 중 하나를 사용 하 여 데이터를 리더 데이터베이스로 수집 합니다.

## <a name="data-provider---share-data"></a>데이터 공급자-데이터 공유

비디오의 지침에 따라 데이터 공유 계정을 만들고, 데이터 집합을 추가 하 고, 초대를 보냅니다.

[![데이터 공급자-데이터 공유](https://img.youtube.com/vi/QmsTnr90_5o/0.jpg)](https://youtu.be/QmsTnr90_5o?&autoplay=1)

## <a name="data-consumer---receive-data"></a>데이터 소비자-데이터 수신

비디오의 지침에 따라 초대를 수락 하 고, 데이터 공유 계정을 만들고, 소비자 클러스터에 매핑합니다.

[![데이터 소비자-데이터 받기](https://img.youtube.com/vi/vBq6iFaCpdA/0.jpg)](https://youtu.be/vBq6iFaCpdA?&autoplay=1)

이제 데이터 소비자는 Azure 데이터 탐색기 클러스터로 이동 하 여 공유 데이터베이스에 대 한 사용자 권한을 부여 하 고 데이터에 액세스할 수 있습니다. 원본 Azure 데이터 탐색기 클러스터에 batch 모드를 사용 하는 데이터 수집 몇 분 내에 대상 클러스터에 표시 됩니다.

## <a name="limitations"></a>제한 사항

* [종동체 DB 제한 사항](follower.md#limitations)

## <a name="next-steps"></a>다음 단계

* [Azure Data Share 설명서](/azure/data-share/)
* 종동체 클러스터에 대 한 자세한 내용은 [종동체 cluster](follower.md) 를 참조 하세요.
