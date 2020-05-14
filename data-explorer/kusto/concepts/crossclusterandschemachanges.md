---
title: 스키마 변경 & kusto 크로스 클러스터 쿼리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 클러스터 간 쿼리 및 스키마 변경에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58fd8cef3836d17eb327734338b1567d815d7349
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382032"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>클러스터 간 쿼리 및 스키마 변경 

클러스터 간 쿼리를 실행할 때 초기 쿼리 해석을 수행 하는 클러스터에는 원격 클러스터에서 참조 되는 엔터티의 스키마가 있어야 합니다.
따라서 다음 쿼리가 *Cluster1* 으로 전송 될 때

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1* 에서 *Table2* 에 포함 된 열과 이러한 열의 데이터 *형식에 대해* 알고 있어야 합니다. *Cluster1* 에서 *클러스터 2*로 특수 명령이 전송 되도록 합니다.
이 명령을 전송 하는 데 비용이 많이 들 수 있으므로, 검색 된 후 원격 엔터티의 스키마가 캐시 되 고 동일한 엔터티를 참조 하는 다음 쿼리는 네트워크 작업을 적게 실행 해야 합니다.

이로 인해 원격 엔터티의 스키마가 변경 될 때 의도 하지 않은 결과가 발생할 수 있습니다 (의미 체계가 아닌 ' 부분 쿼리 오류 '를 야기 하는 열을 인식할 수 없거나 삭제 된 열이 추가 됨).

이 문제를 완화 하기 위해 캐시 된 스키마는 검색 후 1 시간 후에 만료 되므로 변경 후 1 시간 이상 실행 된 모든 쿼리는 최신 스키마로 작동 합니다.