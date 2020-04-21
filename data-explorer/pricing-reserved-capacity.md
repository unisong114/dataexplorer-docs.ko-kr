---
title: 비용을 절감하기 위해 Azure 데이터 탐색기 태그에 대한 선불
description: Azure 데이터 탐색기 비용을 절약하기 위해 Azure 데이터 탐색기 예약 용량을 구입하는 방법에 대해 알아봅니다.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 6475a0190546ac577ae86ac1fde4adc41cd47595
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501738"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Azure 데이터 탐색기 예약 용량을 가진 Azure 데이터 탐색기 태그 단위에 대한 선불

종량제 가격과 비교하여 마크업 단위에 대해 선불로 Azure Data Explorer를 사용하여 비용을 절감합니다. Azure Data Explorer 예약 용량을 사용하면 Azure Data Explorer를 1년 또는 3년 동안 사용하여 Azure Data Explorer 태그 비용을 크게 할인받을 수 있습니다. Azure Data Explorer 예약 용량을 구입하려면 용어를 지정하기만 하면 모든 리전의 Azure Data Explorer의 모든 배포에 적용됩니다.

예약을 구매하면 1년 또는 3년 동안 마크업 비용을 미리 지불합니다. 예약을 구매하는 즉시 예약 속성과 일치하는 Azure Data Explorer 태그 요금은 더 이상 종량제 요금이 청구되지 않습니다. 이미 실행 중이거나 새로 배포된 Azure Data Explorer 클러스터는 자동으로 이점을 얻습니다. 이 예약에는 클러스터와 관련된 계산, 네트워킹 또는 저장소 요금이 다루지 않습니다. 이러한 리소스에 대한 예약 용량은 별도로 구입해야 합니다. 예약 기간이 끝나면 청구 혜택이 만료되고 Azure Data Explorer 마크업 단위는 종량제 가격으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다. 가격 정보는 Azure [데이터 탐색기 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/data-explorer/)참조하십시오.

Azure [포털에서](https://portal.azure.com)Azure 데이터 탐색기 예약 용량을 구입할 수 있습니다. Azure 데이터 탐색기 예약 용량을 구입하려면 다음을 수행하십시오.

* 하나 이상의 엔터프라이즈 또는 종량제 구독의 소유자여야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용하지 않도록 설정한 경우 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 Azure Data Explorer 예약 용량을 구입할 수 있습니다.

기업 고객 및 종량제 고객이 예약 구매에 대해 청구되는 방식에 대한 자세한 내용은 다음을 참조하십시오.
* [엔터프라이즈 등록에서 Azure Reservation 사용량 이해](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [종량제 구독에 대한 Azure 예약 사용량 이해.](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)

## <a name="determine-the-right-markup-usage-before-purchase"></a>구매 하기 전에 올바른 태그 사용 확인

예약 규모는 기존 또는 곧 배포될 Azure Data Explorer 클러스터에서 사용하는 총 Azure Data Explorer 태그 단위 수를 기반으로 해야 합니다. 태그 단위 수는 개발/테스트 SKU를 포함하지 않는 프로덕션 환경에서 Azure Data Explorer 엔진 클러스터 코어 수와 같습니다. 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Azure 데이터 탐색기 예약 용량 구매

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 지금 **모든 서비스** > **예약** > **구매를**선택합니다. **추가** 선택
1. 제품 **유형 선택** 창에서 **Azure 데이터 탐색기를** 선택하여 Azure 데이터 탐색기 태그 단위에 대한 새 예약을 구입합니다. 
1. **구매** 선택
1. 필수 필드를 입력합니다. 

    ![구매 페이지](media/pricing-reserved-capacity/purchase-page.png)

1. **비용** 섹션에서 Azure 데이터 탐색기 마크업 예약 용량 예약의 비용을 검토합니다.
1. **구매**를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

Azure Data Explorer 예약 용량 예약을 취소해야 하는 경우 12%의 조기 종료 수수료가 부과될 수 있습니다. 환불은 구매 가격의 최저 가격 또는 예약의 현재 가격을 기준으로 합니다. 환불은 연간 $50,000로 제한됩니다. 받는 환불은 12%의 조기 종료 수수료를 뺀 나머지 비례 배분한 잔액입니다. 취소를 요청하려면 Azure Portal의 예약으로 이동하고 **환불**을 선택하여 지원 요청을 만듭니다.

Azure Data Explorer 예약 용량 예약을 다른 용어로 변경해야 하는 경우 동일하거나 더 큰 가치가 있는 다른 예약으로 교환할 수 있습니다. 새 예약에 대한 기간 시작일은 교환된 예약에서 수행되지 않습니다. 새 예약을 만들 때 1년 또는 3년의 기간이 시작됩니다. 교환을 요청하려면 Azure Portal의 예약으로 이동하고, **교환**을 선택하여 지원 요청을 만듭니다.

예약을 교환하거나 환불하는 방법에 대한 자세한 내용은 [예약 교환 및 환불을](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)참조하십시오.

## <a name="manage-your-reserved-capacity-reservation"></a>예약된 용량 예약 관리

Azure Data 탐색기 마크업 단위 예약 할인은 Azure Data Explorer 예약 용량 예약 범위 및 특성과 일치하는 태그 단위 수에 자동으로 적용됩니다. 


> [!NOTE]
> * Azure [포털,](https://portal.azure.com)PowerShell, CLI 또는 API를 통해 Azure 데이터 탐색기 예약 용량 예약의 범위를 업데이트할 수 있습니다.
> * Azure Data Explorer 예약 용량 예약을 관리하는 방법을 알아보려면 [Azure Data Explorer 예약 용량 관리를](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Reservations란?](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [Azure Reservations 관리](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [Azure 예약 할인 이해](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [종량제 구독의 예약 사용량 이해](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [엔터프라이즈 등록에서 예약 사용량 이해](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
