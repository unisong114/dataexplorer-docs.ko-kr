---
title: 엔터티 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 엔터티에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d31765d72d37b0146cf7ba8a42e02722296bf80e
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663479"
---
# <a name="entities"></a>엔터티

Kusto 쿼리는 Kusto 클러스터에 연결된 일부 Kusto 데이터베이스의 컨텍스트에서 실행됩니다. 데이터베이스의 데이터는 쿼리에서 참조할 수 있는 테이블에 정렬되며, 테이블 내에서 열과 행의 사각형 그리드로 구성됩니다. 또한 쿼리는 다시 사용할 수 있는 쿼리 조각인 데이터베이스의 저장 함수를 참조할 수 있습니다.

* 클러스터는 데이터베이스를 유지하는 엔터티입니다.
  클러스터에는 이름이 없지만 클러스터의 URI에서 `cluster()` 특수 함수를 사용하여 참조할 수 있습니다.
  예를 들어 `cluster("https://help.kusto.windows.net")`는 `Samples` 데이터베이스를 유지하는 클러스터에 대한 참조입니다.

* [데이터베이스](./databases.md)는 테이블과 저장 함수를 유지하는 명명된 엔터티입니다. 모든 Kusto 쿼리는 일부 데이터베이스의 컨텍스트에서 실행되며, 해당 데이터베이스의 엔터티는 자격 없이 쿼리에서 참조할 수 있습니다. 또한 클러스터의 다른 데이터베이스 또는 다른 클러스터의 데이터베이스는 [database() 특수 함수](../databasefunction.md)를 사용하여 참조할 수 있습니다. 예를 들어 `cluster("https://help.kusto.windows.net").database("Samples")`
  특정 데이터베이스에 대한 범용 참조입니다.

* [테이블](./tables.md)은 데이터를 유지하는 명명된 엔터티입니다. 테이블에는 정렬된 열 세트와 0개 이상의 데이터 행이 있으며, 각 행은 테이블의 각 열에 대한 하나의 데이터 값을 유지합니다. 테이블은 쿼리의 컨텍스트에서 데이터베이스에 있는 경우에만 이름으로 참조할 수 있으며, 그렇지 않은 경우 데이터베이스 참조를 사용하여 정규화할 수 있습니다. 예를 들어 `cluster("https://help.kusto.windows.net").database("Samples").StormEvents`는 `Samples` 데이터베이스의 특정 테이블에 대한 범용 참조입니다.
  또한 테이블은 [table() 특수 함수](../tablefunction.md)를 사용하여 참조할 수도 있습니다.

* [열](./columns.md)은 [스칼라 데이터 형식](../scalar-data-types/index.md)의 명명된 엔터티입니다.
  열은 해당 열을 참조하는 특정 연산자의 컨텍스트에 있는 테이블 형식 데이터 스트림을 기준으로 쿼리에서 참조됩니다.

* [저장 함수](./stored-functions.md)는 Kusto 쿼리 또는 쿼리 부분을 다시 사용할 수 있는 명명된 엔터티입니다.

* [외부 테이블](./externaltables.md)은 Kusto 데이터베이스 외부에 저장된 데이터를 참조하는 엔터티입니다.
  외부 테이블은 Kusto에서 외부 스토리지로 데이터를 내보내고 Kusto로 수집하지 않고 외부 데이터를 쿼리하는 데에도 사용됩니다.