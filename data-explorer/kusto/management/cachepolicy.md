---
title: 캐시 정책(핫 캐시 및 콜드 캐시) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 핫 및 콜드 캐시에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 591763ac5d94a8361a4b78c1b199bb05299cc004
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522137"
---
# <a name="cache-policy-hot-and-cold-cache"></a>캐시 정책(핫 캐시 및 콜드 캐시)

Azure Data Explorer는 수집된 데이터를 실제 처리(예: Azure Compute) 노드에서 벗어나 신뢰할 수 있는 저장소(가장 일반적으로 Azure Blob Storage)에 저장합니다. 해당 데이터에 대한 쿼리 속도를 높이기 위해 Azure Data Explorer는 처리 노드, SSD 또는 RAM에서도 이 데이터(또는 데이터의 일부)를 캐시합니다. Azure Data Explorer에는 캐시할 데이터 개체를 지능적으로 결정하도록 설계된 정교한 캐시 메커니즘이 포함되어 있습니다. 캐시를 사용하면 Azure Data Explorer에서 사용하는 데이터 아티팩트(예: 열 인덱스 및 열 데이터 샤드)를 설명하여 보다 "중요한" 데이터를 우선 순위를 지정할 수 있습니다.

수집된 모든 데이터가 캐시될 때 최상의 쿼리 성능이 달성되지만 특정 데이터가 로컬 SSD 저장소에서 "웜"을 유지하는 비용을 정당화하지 않는 경우가 많습니다.
예를 들어, 많은 팀에서는 거의 액세스되지 않은 이전 로그 레코드의 중요성이 낮다고 생각합니다.
항상 따뜻하게 유지하기 위해 비용을 지불하는 대신 이 데이터를 쿼리할 때 성능이 저하되는 것을 선호합니다.

Azure Data Explorer 캐시는 고객이 핫 데이터 캐시 **및** 콜드 **데이터 캐시의** 두 가지 데이터 캐시 정책을 구분하는 데 사용할 수 있는 세분화된 **캐시 정책을**제공합니다. Azure Data Explorer 캐시는 핫 데이터 캐시의 정의된 크기까지 로컬 SSD(또는 RAM)의 핫 데이터 캐시에 속하는 모든 데이터를 유지하려고 시도합니다. 나머지 로컬 SSD 공간은 핫으로 분류되지 않은 데이터를 보유하는 데 사용됩니다. 이 디자인의 한 가지 유용한 의미는 신뢰할 수 있는 저장소에서 많은 콜드 데이터를 로드하는 쿼리가 핫 데이터 캐시에서 데이터를 제거하지 않는다는 것입니다. 따라서 핫 데이터 캐시의 데이터와 관련된 쿼리에는 큰 영향을 미치지 않습니다.

핫 캐시 정책을 설정하는 주요 의미는 다음과 같습니다.
* **비용** 신뢰할 수 있는 저장소의 비용은 로컬 SSD보다 훨씬 낮을 수 있습니다(예: Azure에서는 현재 약 45배 저렴합니다).
* **성능** 로컬 SSD에 있을 때 데이터를 더 빠르게 쿼리할 수 있습니다. 이는 특히 범위 쿼리, 즉 대량의 데이터를 검색하는 쿼리의 경우 그렇습니다.  

[제어 명령을](cache-policy.md) 사용하면 관리자가 캐시 정책을 관리할 수 있습니다.

## <a name="how-the-cache-policy-gets-applied"></a>캐시 정책이 적용되는 방법

데이터가 Azure Data Explorer로 수집되면 시스템은 수집이 수행되고 범위가 만들어진 날짜/시간을 추적합니다. 익스텐트의 섭취 날짜/시간 값(또는 여러 기존 익스텐트에서 익스텐션이 빌드된 경우 최대값)을 사용하여 캐시 정책을 평가합니다.

기본적으로 유효 정책은 `null`모든 데이터가 **핫으로**간주됩니다.
비`null` 테이블 수준 정책은 데이터베이스 수준 정책을 재정의합니다.

> [!Note] 
> 인스티션 속성을 `creationTime`사용하여 인스내션 날짜/시간에 대한 값을 지정할 수 있습니다. 

## <a name="scoping-queries-to-hot-cache"></a>핫 캐시에 대한 쿼리 범위 지정

Kusto는 핫 캐시 데이터로만 범위가 조정된 쿼리를 지원합니다. 다음과 같은 여러 가지 방법으로 이 작업을 수행할 수 있습니다.

- 쿼리 가능한 값에 `query_datascope` 호출된 클라이언트 `default`요청 `all`속성을 `hotcache`추가합니다.
- 쿼리 `set` 텍스트에서 문을 사용합니다: `set query_datascope='...'`" 가능한 값은 클라이언트 요청 속성과 동일합니다.
- 쿼리 `datascope=...` 본문에서 테이블 참조 직후에 텍스트를 추가합니다. 가능한 값은 `all` 및 `hotcache`입니다.

이 `default` 값은 쿼리가 모든 데이터를 포함해야 한다고 결정하는 클러스터 기본 설정의 사용을 나타냅니다.



다른 메서드 간에 불일치가 있는 경우: `set` 클라이언트 요청 속성보다 우선하며 테이블 참조에 대한 값을 지정하는 것이 둘 다보다 우선합니다.

예를 들어 다음 쿼리에서 모든 테이블 참조는 모든 데이터에 대한 범위가 `T` 조정되는 두 번째 참조를 제외하고 핫캐시 데이터만 사용합니다.

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>캐시 정책 대 보존 정책

캐시 정책은 [보존 정책과](./retentionpolicy.md)독립적입니다. 
- 캐시 정책은 중요 데이터에 대한 쿼리가 덜 중요한 데이터에 대한 쿼리의 영향에 더 빠르고 저항할 수 있도록 리소스의 우선 순위를 지정하는 방법을 정의합니다. 
- 보존 정책은 `SoftDeletePeriod`테이블/데이터베이스(특히)에서 쿼리 가능한 데이터의 범위를 정의합니다.

예상 쿼리 패턴에 따라 비용과 성능 간의 최적의 균형을 이루도록 이 정책을 구성하는 것이 좋습니다.

예:
* `SoftDeletePeriod`= 56d
* `hot cache policy`= 28d

이 예제에서는 지난 28일간의 데이터가 클러스터 SSD에 저장되고 **추가** 28일이 Azure Blob 저장소에 저장됩니다. 전체 56일 동안 의 쿼리를 실행할 수 있습니다. 

## <a name="cache-policy-does-not-make-kusto-a-cold-storage-technology"></a>캐시 정책은 Kusto를 콜드 스토리지 기술로 만들지 않습니다.

Azure Data Explorer는 임시 쿼리를 수행하도록 설계되었으며 중간 결과 집합은 클러스터의 총 RAM에 적합합니다. 맵 축소와 같은 대규모 작업의 경우 중간 결과를 영구 저장소(예: SSD)에 저장하려는 경우 연속 내보내기 기능을 사용합니다. 이를 통해 HDInsight 또는 Azure Databricks와 같은 서비스를 사용하여 장기 실행 배치 쿼리를 수행할 수 있습니다.