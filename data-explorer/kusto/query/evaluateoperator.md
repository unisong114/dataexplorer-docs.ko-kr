---
title: 플러그인 연산자 평가 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 플러그인 연산자 평가에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1aae36df29abf705ba821abdc2d1da96e4635a60
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515745"
---
# <a name="evaluate-plugin-operator"></a>evaluate plugin 연산자

서비스 측 쿼리 확장(플러그인)을 호출합니다.

연산자는 `evaluate` 플러그인으로 알려진 쿼리 언어 확장을 호출하는 기능을 제공하는 테이블 **연산자입니다.** 플러그인은 항상 사용할 수 있는 다른 언어 구문과 달리 활성화 또는 비활성화할 수 있으며 언어의 관계적 특성에 의해 "바인딩"되지 않습니다(예: 미리 정의된 정적 결정, 출력 스키마가 없을 수 있음).

**구문** 

[*T* `|`T `evaluate` ] [ *[평가매개 변수* ] 플러그인`,` *이름* `(` [*PluginArg1* [ *플러그인Arg2*]...`)`

위치:

* *T는* 플러그인에 대한 선택적 테이블 형식 입력입니다. (일부 플러그인은 입력을 받지 않으며 테이블 형식 데이터 원본역할을 합니다.)
* *플러그인이름은* 호출되는 플러그인의 필수 이름입니다.
* *플러그인Arg1*, ... 플러그인에 대한 선택적 인수입니다.
* *evaluateParameters*: 평가 작업 및 실행 계획의 동작을 제어하는 *이름* `=` *값* 의 형태로 0 개 이상의 (공간 분리) 매개 변수입니다. 다음 매개 변수가 지원됩니다. 

  |이름                |값                           |Description                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [배포 힌트](#distribution-hints) |

**참고 사항**

* 구문적으로, `evaluate` 테이블 함수를 호출 호출 [연산자와](./invokeoperator.md)유사하게 작동합니다.
* 평가 연산자를 통해 제공되는 플러그인은 쿼리 실행 또는 인수 평가의 일반 규칙에 구속되지 않습니다.
특정 플러그인에는 특정 제한 사항이 있을 수 있습니다. 예를 들어 출력 스키마가 데이터에 종속된 플러그인(예: [bag_unpack 플러그인)은](./bag-unpackplugin.md)클러스터 간 쿼리를 수행할 때 사용할 수 없습니다.

## <a name="distribution-hints"></a>배포 힌트

배포 힌트는 플러그인 실행을 여러 클러스터 노드에 분산하는 방법을 지정합니다. 각 플러그인은 배포에 대해 다른 지원을 구현할 수 있습니다. 플러그인의 설명서는 플러그인에서 지원하는 배포 옵션을 지정합니다.

가능한 값은 다음과 같습니다.

* `single`: 플러그인의 단일 인스턴스가 전체 쿼리 데이터를 통해 실행됩니다.
* `per_node`: 플러그인 호출 전에 쿼리가 노드 간에 분산되면 플러그인인스턴스가 포함된 데이터를 통해 각 노드에서 실행됩니다.
* `per_shard`: 플러그인 호출 전의 데이터가 샤드에 분산되면 플러그인 인스턴스가 데이터의 각 샤드를 통해 실행됩니다.