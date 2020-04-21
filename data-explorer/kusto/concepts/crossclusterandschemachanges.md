---
title: 클러스터 간 쿼리 및 스키마 변경 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 클러스터 간 쿼리 및 스키마 변경 사항에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9473add657fe8a888818f83c72f12d47138c00e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523293"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>클러스터 간 쿼리 및 스키마 변경 

클러스터 간 쿼리를 실행할 때 초기 쿼리 해석을 수행하는 클러스터는 원격 클러스터에서 참조되는 엔터티의 스키마가 있어야 합니다.
따라서 다음 쿼리가 *Cluster1로* 전송되는 경우

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1은* *Cluster2의* *Table2에* 있는 열과 해당 열의 데이터 형식을 알아야 합니다. 특수 명령이 *Cluster1에서* *Cluster2로*전송됩니다.
이 명령을 보내는 것은 비용이 많이 들 수 있으므로 일단 검색되면 원격 엔터티에 대한 스키마가 캐시되고 동일한 엔터티를 참조하는 다음 쿼리는 네트워크 작업을 더 적게 실행해야 합니다.

이렇게 하면 원격 엔터티스키마가 변경될 때 원치 않는 효과가 발생할 수 있습니다(추가된 열이 인식되지 않거나 열이 삭제되어 의미 체계 오류 대신 '부분 쿼리 오류'가 발생).

이 문제를 완화하기 위해 캐시된 스키마는 검색 후 1시간 후에 만료되므로 변경 후 1시간 이상 실행된 모든 쿼리는 최신 스키마와 함께 작동합니다.