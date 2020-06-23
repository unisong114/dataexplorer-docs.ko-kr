---
title: 플러그 인 연산자 평가-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 플러그 인 연산자를 평가 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: d01b3b5178801fe1b5e55f51987564674ce4aeae
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128634"
---
# <a name="evaluate-operator-plugins"></a>연산자 플러그 인 평가

서비스 측 쿼리 확장 (플러그 인)을 호출 합니다.

`evaluate`연산자는 **플러그 인**이라고 하는 쿼리 언어 확장을 호출 하는 기능을 제공 하는 테이블 형식 연산자입니다. 플러그 인은 사용 하거나 사용 하지 않도록 설정할 수 있으며 (항상 사용할 수 있는 다른 언어 구문과 달리) 언어의 관계형 특성에 의해 "바인딩" 되지 않습니다. 예를 들어 미리 정의 된 정적으로 확인 된 출력 스키마가 없을 수도 있습니다.

**구문** 

[*T* `|` ] `evaluate` [ *evaluateParameters* ] *pluginname* `(` [*PluginArg1* [ `,` *PluginArg2*] ...`)`

위치:

* *T* 는 플러그 인에 대 한 선택적 테이블 형식 입력입니다. 일부 플러그 인은 입력을 취하지 않으며 테이블 형식 데이터 소스 역할을 합니다.
* *Pluginname* 은 호출 되는 플러그 인의 필수 이름입니다.
* *PluginArg1*, ... 플러그 인에 대 한 선택적 인수입니다.
* *evaluateParameters*: *Name* `=` 계산 작업 및 실행 계획의 동작을 제어 하는 이름 *값* 형식의 0 개 이상의 (공백으로 구분 된) 매개 변수입니다. 각 플러그 인은 각 매개 변수를 처리 하는 방법을 다르게 결정할 수 있습니다. 특정 동작에 대 한 각 플러그 인의 설명서를 참조 하세요.  

지원 되는 매개 변수는 다음과 같습니다. 

  |Name                |값                           |설명                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [배포 힌트](#distributionhints) |
  |`hint.pass_filters` |`true`, `false`| `evaluate`연산자가 플러그 인 이전에 일치 하는 필터를 통과 하도록 허용 합니다. 필터는 연산자 이전에 기존 열을 참조 하는 경우 ' 일치 ' 된 것으로 간주 됩니다 `evaluate` . 기본값: `false` |
  |`hint.pass_filters_column` |*column_name*| 플러그 인 연산자가 플러그 인 이전에 *column_name* 를 참조 하는 통과 필터를 허용 합니다. 매개 변수는 다른 열 이름으로 여러 번 사용할 수 있습니다. |

**참고 사항**

* 구문상 `evaluate` 은 테이블 형식 함수를 호출 하는 [invoke 연산자](./invokeoperator.md)와 유사 하 게 동작 합니다.
* Evaluate 연산자를 통해 제공 되는 플러그 인은 쿼리 실행 또는 인수 평가의 일반 규칙에 의해 바인딩되지 않습니다.
* 특정 플러그 인에는 특정 제한 사항이 있을 수 있습니다. 예를 들어 클러스터 간 쿼리를 수행할 때는 출력 스키마가 데이터에 종속 된 플러그 인 (예: [bag_unpack 플러그](./bag-unpackplugin.md) 인 및 [피벗 플러그 인](./pivotplugin.md))을 사용할 수 없습니다.

<a id="distributionhints"/>**배포 힌트**</a>

배포 힌트는 여러 클러스터 노드에 플러그 인 실행을 배포 하는 방법을 지정 합니다. 각 플러그 인은 배포에 대해 다른 지원을 구현할 수 있습니다. 플러그 인의 설명서는 플러그 인에서 지원 되는 배포 옵션을 지정 합니다.

가능한 값은 다음과 같습니다.

* `single`: 플러그 인의 단일 인스턴스는 전체 쿼리 데이터에 대해 실행 됩니다.
* `per_node`: 플러그 인 호출 이전의 쿼리가 여러 노드에 분산 된 경우 플러그 인 인스턴스가 포함 된 데이터에 대해 각 노드에서 실행 됩니다.
* `per_shard`: 플러그 인 호출 전의 데이터가 분할 간에 분산 되는 경우 플러그 인의 인스턴스는 데이터의 각 분할 된 데이터에 대해 실행 됩니다.
