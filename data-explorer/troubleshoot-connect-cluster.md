---
title: Azure 데이터 탐색기 클러스터 연결 오류 문제 해결
description: 이 문서에서는 Azure 데이터 탐색기 클러스터에 연결 하기 위한 문제 해결 단계를 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 406e41728b34d0b0de48d71b1d7c2cf7ce0098bc
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874106"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>문제 해결: Azure 데이터 탐색기에서 클러스터에 연결하지 못함

Azure 데이터 탐색기에서 클러스터에 연결할 수 없다면 다음 단계를 수행합니다.

1. 연결 문자열이 올바른지 확인합니다. `https://<ClusterName>.<Region>.kusto.windows.net` 형식이어야 합니다. 다음 예와 같습니다. `https://docscluster.westus.kusto.windows.net`

1. 적절한 권한이 있는지 확인합니다. 권한이 없다면 *권한 없음*의 응답을 받게 됩니다.

    권한에 대한 자세한 내용은 [데이터베이스 권한 관리](manage-database-permissions.md)를 참조하세요. 필요한 경우 적절한 역할에 추가할 수 있도록 클러스터 관리자와 함께 작업하세요.

1. 클러스터가 삭제되지 않았는지 확인합니다. 구독에서 활동 로그를 검토합니다.

1. [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)를 확인하세요. 클러스터에 연결하려는 지역에서 Azure 데이터 탐색기의 상태를 찾습니다.

    상태가 **양호** (녹색 확인 표시)이 아니면 상태가 개선 된 후 클러스터에 연결 해 봅니다.

1. 문제를 해결하는 데 여전히 지원이 필요한 경우 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 지원 요청을 여세요.