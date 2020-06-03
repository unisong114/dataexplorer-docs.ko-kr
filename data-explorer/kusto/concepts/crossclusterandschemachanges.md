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
ms.openlocfilehash: 9ed8ba6ac5e66326e6aa1d9e7a7f474506b57e26
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328979"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>클러스터 간 쿼리 및 스키마 변경

클러스터 간 쿼리를 실행할 때 초기 쿼리 해석을 수행 하는 클러스터에는 원격 클러스터에서 참조 되는 엔터티의 스키마가 있어야 합니다.
다음 쿼리를 *Cluster1* 로 보내는 경우

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1* 에는 *Table2* *의 열* 과 해당 열의 데이터 형식이 무엇 인지 알고 있어야 합니다. 이 정보를 얻기 위해 *Cluster1* 에서 *클러스터 2*로 특수 명령이 전송 됩니다.
명령을 전송 하는 데 비용이 많이 들 수 있습니다. 검색 되 면 원격 엔터티의 스키마가 캐시 되 고 동일한 엔터티를 참조 하는 다음 쿼리는 더 작은 네트워크 작업을 실행 합니다.

원격 엔터티의 스키마를 변경 하면 원치 않는 결과가 발생할 수 있습니다. 예를 들어 추가 된 열이 인식 되지 않거나 삭제 된 열은 의미 체계 오류 대신 ' 부분 쿼리 오류 '를 발생 시킵니다.

이 문제를 해결 하기 위해 캐시 된 스키마는 검색 후 한 시간 후에 만료 되므로 변경 후 1 시간 이상 실행 된 모든 쿼리는 최신 스키마를 사용 하 여 작동 합니다.
