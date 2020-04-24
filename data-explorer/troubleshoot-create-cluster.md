---
title: Azure 데이터 탐색기 클러스터 생성 실패 문제 해결
description: 이 문서에서는 Azure 데이터 탐색기에서 클러스터를 만들기 위한 문제 해결 단계에 대해 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501088"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>문제 해결: Azure 데이터 탐색기의 클러스터 생성 실패

예상치 않게 Azure 데이터 탐색기에서 클러스터를 만들지 못한 경우에는 다음 단계를 수행합니다.

1. 적절한 권한이 있는지 확인합니다. 클러스터를 만들려면 Azure 구독에 대한 ‘참가자’ 또는 ‘소유자’ 역할의 구성원이어야 합니다.**** 필요한 경우 사용자는 구독 관리자와 협력하여 해당 역할에 추가될 수 있습니다.

1. Azure Portal의 **클러스터 만들기** 아래에 입력한 클러스터 이름과 관련된 유효성 검사 오류가 없는지 확인합니다.

1. [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)를 확인하세요. 클러스터를 만들려는 지역에서 Azure 데이터 탐색기의 상태를 검색합니다.

    상태가 **양호**(녹색 확인 표시)가 아닌 경우 상태가 개선된 후 클러스터를 만들어 보세요.

1. 문제를 해결하는 데 여전히 지원이 필요한 경우 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 지원 요청을 여세요.
